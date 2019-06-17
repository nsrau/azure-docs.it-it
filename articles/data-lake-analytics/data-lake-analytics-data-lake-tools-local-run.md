---
title: Eseguire script U-SQL di Azure Data Lake nel computer locale
description: Informazioni su come usare Strumenti Azure Data Lake per Visual Studio per eseguire i processi U-SQL sul computer locale.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62113936"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Eseguire script U-SQL nel computer locale

Quando si sviluppano script U-SQL, è possibile risparmiare tempo e costi eseguendo gli script in locale. Strumenti Azure Data Lake per Visual Studio supporta l'esecuzione di script U-SQL nel computer locale. 

## <a name="basic-concepts-for-local-runs"></a>Concetti di base per le esecuzioni locali

Il grafico seguente mostra i componenti per l'esecuzione locale e i corrispondenti di questi componenti per l'esecuzione su cloud.

|Componente|Esecuzione locale|Esecuzione su cloud|
|---------|---------|---------|
|Archiviazione|Cartella radice dei dati locale|Account di Azure Data Lake Store predefinito|
|Calcolo|Motore di esecuzione locale di U-SQL|Servizio Azure Data Lake Analytics|
|Ambiente di esecuzione|Directory di lavoro sul computer locale|Cluster di Azure Data Lake Analytics|

Le sezioni seguenti forniscono altre informazioni sui componenti dell'esecuzione locale.

### <a name="local-data-root-folders"></a>Cartelle radice dei dati locali

Una cartella radice dei dati locale è un **archivio locale** per l'account di calcolo locale. Qualsiasi cartella nel file system locale del computer locale può essere una cartella radice dei dati locale. Equivale all'account di Azure Data Lake Store predefinito di un account di Data Lake Analytics. Il passaggio a una cartella radice dei dati diversa equivale al passaggio a un account di archiviazione predefinito diverso. 

La cartella radice dei dati viene usata per:
- Archiviare metadati. Ad esempio, database, tabelle, funzioni con valori di tabella e assembly.
- Cercare i percorsi di input e output definiti come percorsi relativi negli script U-SQL. L'uso di percorsi relativi semplifica la distribuzione degli script U-SQL in Azure.

### <a name="u-sql-local-run-engines"></a>Motori di esecuzione locale di U-SQL

Un motore di esecuzione locale di U-SQL è un **account di calcolo locale** per i processi U-SQL. Gli utenti possono eseguire i processi U-SQL in locale mediante Strumenti Azure Data Lake per Visual Studio. Le esecuzioni locali sono supportate anche tramite le interfacce di programmazione e della riga di comando dell'SDK U-SQL di Azure Data Lake. Leggere altre informazioni sull'[SDK U-SQL di Azure Data Lake](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Directory di lavoro

Quando si esegue uno script U-SQL, è necessaria una cartella directory di lavoro per memorizzare nella cache i risultati di compilazione e i log di esecuzione e per eseguire altre funzioni. In Strumenti Azure Data Lake per Visual Studio la directory di lavoro è la directory di lavoro del progetto U-SQL. La directory si trova in `<U-SQL project root path>/bin/debug>`. La directory di lavoro viene pulita ogni volta che viene attivata una nuova esecuzione.

## <a name="local-runs-in-microsoft-visual-studio"></a>Esecuzioni locali in Microsoft Visual Studio

Strumenti Azure Data Lake per Visual Studio include un motore di esecuzione locale predefinito. Gli strumenti trattano il motore come account di calcolo locale. Per eseguire uno script U-SQL in locale, selezionare l'account **Local-machine** o **Local-project** nell'elenco a discesa a margine dell'editor di script. Selezionare quindi **Invia**.

![Inviare uno script U-SQL a un account locale](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Esecuzioni locali con un account Local-machine

Un account **Local-machine** è un account di calcolo locale condiviso con una singola cartella radice dei dati locale come account di archiviazione locale. Per impostazione predefinita, la cartella radice dei dati si trova in **C:\Utenti\<nome utente>\AppData\Local\USQLDataRoot**. È possibile eseguire la configurazione tramite **Strumenti** > **Data Lake** > **Opzioni e impostazioni**.

![Configurare una cartella radice dei dati locale](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Per un'esecuzione locale è necessario un progetto U-SQL. La directory di lavoro del progetto U-SQL viene usata per la directory di lavoro dell'esecuzione locale di U-SQL. I risultati di compilazione, i log di esecuzione e altri file correlati all'esecuzione del processo vengono generati e archiviati nella cartella directory di lavoro durante l'esecuzione locale. Ogni volta che si esegue di nuovo lo script, tutti i file nella directory di lavoro vengono puliti e rigenerati.

## <a name="local-runs-with-a-local-project-account"></a>Esecuzioni locali con un account Local-project

Un account **Local-project** è un account di calcolo locale isolato dal progetto per ogni progetto con una cartella radice dei dati locale isolata. Ogni progetto U-SQL attivo che si apre in Esplora soluzioni in Visual Studio ha un account `(Local-project: <project name>)` corrispondente. Gli account sono elencati sia in Esplora server in Visual Studio che a margine dell'editor di script U-SQL.  

L'account **Local-project** offre un ambiente di sviluppo isolato e pulito. Un account **Local-machine** ha una cartella radice dei dati locale condivisa che archivia i metadati e i dati di input e output per tutti i processi locali. Un account **Local-project** crea invece una cartella radice dei dati locale temporanea in una directory di lavoro del progetto U-SQL ogni volta che viene eseguito uno script U-SQL. Questa cartella radice dei dati temporanea viene pulita a ogni nuova compilazione o esecuzione. 

Un progetto U-SQL gestisce l'ambiente di esecuzione locale isolato mediante una proprietà e un riferimento al progetto. È possibile configurare le origini dati di input per gli script U-SQL sia nel progetto che negli ambienti di database di riferimento.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Gestire l'origine dati di input per un account Local-project 

Un progetto U-SQL crea una cartella radice dei dati locale e configura i dati per un account **Local-project**. Una cartella radice dei dati temporanea viene pulita e ricreata nella directory di lavoro del progetto U-SQL a ogni nuova compilazione ed esecuzione locale. Tutte le origini dati configurate dal progetto U-SQL vengono copiate in questa cartella radice dei dati locale temporanea prima delle esecuzioni dei processi locali. 

È possibile configurare la cartella radice delle origini dati. Fare clic con il pulsante destro del mouse su **Progetto U-SQL** > **Proprietà** > **Verifica origine dati**. Quando si esegue uno script U-SQL in un account **Local-project**, tutti i file e le sottocartelle nella cartella **Verifica origine dati** vengono copiati nella cartella radice dei dati locale temporanea. Sono inclusi i file nelle sottocartelle. Al termine delle esecuzioni dei processi locali, i risultati di output sono disponibili anche nella cartella radice dei dati locale temporanea nella directory di lavoro del progetto. Tutto l'output viene eliminato e pulito a ogni nuova compilazione e pulizia del progetto. 

![Configurare l'origine dati di test di un progetto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Gestire un ambiente di database di riferimento per un account **Local-project** 

Se una query U-SQL usa oggetti di database U-SQL o se si esegue una query con tali oggetti, è necessario preparare gli ambienti di database in locale prima di eseguire lo script U-SQL in locale. Per un account **Local-project**, le dipendenze del database U-SQL possono essere gestite tramite i riferimenti al progetto U-SQL. È possibile aggiungere i riferimenti al progetto del database U-SQL al proprio progetto U-SQL. Prima di eseguire script U-SQL in un account **Local-project**, tutti i database di riferimento vengono distribuiti nella cartella radice dei dati locale temporanea. Per ogni esecuzione, la cartella radice dei dati temporanea viene pulita come un nuovo ambiente isolato.

Vedere l'articolo correlato seguente:
* Leggere le informazioni su come gestire i riferimenti e le definizioni di database U-SQL nei [progetti di database U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Differenza tra account **Local-machine** e **Local-project**

Un account **Local-machine** simula un account di Azure Data Lake Analytics nei computer locali degli utenti. Condivide la stessa esperienza con l'account di Azure Data Lake Analytics. Un account **Local-project** offre un ambiente di sviluppo locale semplice da usare. Questo ambiente consente agli utenti di distribuire i riferimenti di database e i dati di input prima dell'esecuzione locale degli script. Un account **Local-machine** offre un ambiente permanente condiviso, a cui è possibile accedere da tutti i progetti. Un account **Local-project** offre un ambiente di sviluppo isolato per ogni progetto. Viene aggiornato a ogni esecuzione. Un account **Local-project** offre un'esperienza di sviluppo più veloce grazie alla rapida applicazione delle nuove modifiche.

Altre differenze tra gli account **Local-machine** e **Local-project** sono illustrate nella tabella seguente:

|Tipo di differenza|Local-machine|Local-project|
|----------------|---------------|---------------|
|Accesso locale|È accessibile da tutti i progetti.|Solo il progetto corrispondente può accedere a questo account.|
|Cartella radice dei dati locale|Cartella locale permanente. Configurata tramite **Strumenti** > **Data Lake** > **Opzioni e impostazioni**.|Una cartella temporanea creata per ogni esecuzione locale nella directory di lavoro del progetto U-SQL. La cartella viene pulita a ogni nuova compilazione o esecuzione.|
|Dati di input per uno script U-SQL|Percorso relativo nella cartella radice dei dati locale permanente.|Eseguire l'impostazione tramite **Proprietà del progetto U-SQL** > **Verifica origine dati**. Tutti i file e le sottocartelle vengono copiati nella cartella radice dei dati temporanea prima di un'esecuzione locale.|
|Dati di output per uno script U-SQL|Percorso relativo nella cartella radice dei dati locale permanente.|Inviati alla cartella radice dei dati temporanea. I risultati vengono puliti a ogni nuova compilazione o esecuzione.|
|Distribuzione del database di riferimento|I database di riferimento non vengono distribuiti automaticamente quando l'esecuzione avviene in un account **Local-machine**. Equivale all'invio a un account di Azure Data Lake Analytics.|I database di riferimento vengono distribuiti automaticamente nell'account **Local-project** prima di un'esecuzione locale. Tutti gli ambienti di database vengono puliti e ridistribuiti a ogni nuova compilazione o esecuzione.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Esecuzione locale con l'SDK U-SQL

È possibile eseguire script U-SQL in locale in Visual Studio e anche usare l'SDK U-SQL di Azure Data Lake per eseguire script U-SQL in locale con le interfacce della riga di comando e di programmazione. Tramite queste interfacce, è possibile automatizzare i test e le esecuzioni locali di U-SQL.

Leggere altre informazioni sull'[SDK U-SQL di Azure Data Lake](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare una pipeline di CI/CD per Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Testare il codice Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
