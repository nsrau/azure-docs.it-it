---
title: Eseguire il debug del codice C# definito dall'utente per i processi U-SQL di Azure Data Lake non riusciti | Microsoft Docs
description: Informazioni su come eseguire il debug di un vertice con errore di U-SQL usando Strumenti Azure Data Lake per Visual Studio.
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/31/2017
ms.author: yanacai
ms.openlocfilehash: 8b16fda041663160c62710cabbe0cd2bd4a83d1e
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Eseguire il debug del codice C# definito dall'utente per i processi U-SQL non riusciti

U-SQL fornisce un modello di estendibilità che usa C#. Negli script U-SQL è possibile chiamare facilmente le funzioni C# ed eseguire funzioni di analisi non supportate dal linguaggio dichiarativo simile a SQL. Per altre informazioni sull'estendibilità di U-SQL, vedere [Guida alla programmabilità di U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

In pratica, qualsiasi codice può richiedere il debug, ma è difficile eseguire il debug di un processo distribuito con il codice personalizzato nel cloud con file di log limitati. [Strumenti Azure Data Lake per Visual Studio](http://aka.ms/adltoolsvs) include una funzionalità denominata **debug del vertice con errore**, che consente di eseguire più facilmente il debug degli errori che si verificano nel codice personalizzato. Quando il processo U-SQL non riesce, il servizio mantiene lo stato di errore e lo strumento consente di scaricare l'ambiente dell'errore cloud nel computer locale per il debug. Il download locale acquisisce tutto l'ambiente cloud, inclusi eventuali dati di input e il codice utente.

Nel video seguente viene illustrata la funzione Debug del vertice con errore in Strumenti Azure Data Lake per Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio richiede i due aggiornamenti seguenti per usare questa funzionalità: [Aggiornamento di Microsoft Visual C++ 2015 Redistributable 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e [Universal C Runtime per Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Scaricare il vertice con errori nel computer locale

Quando si apre un processo non riuscito in Strumenti Azure Data Lake per Visual Studio, viene visualizzata una barra di avviso gialla con messaggi di errore dettagliati nella scheda errore.

1. Fare clic su **Scarica** per scaricare tutte le risorse e i flussi di input necessari. Se il download non viene completato, fare clic su **Riprova**.

2. Fare clic su **Apri** dopo aver completato il download per generare l'ambiente di debug locale. Viene creata e aperta automaticamente una nuova istanza di Visual Studio con una soluzione di debug.

![Vertice download visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Configurare l'ambiente di debug

> [!NOTE]
> Prima del debug, assicurarsi di selezionare le **Common Language Runtime Exceptions** (Eccezioni di Common Language Runtime) nella finestra Impostazioni eccezioni (**CTRL + ALT + E**).

![Impostazione visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Nella nuova istanza di Visual Studio avviata è possibile trovare o meno il codice sorgente C# definito dall'utente:

1. [È possibile trovate il codice sorgente nella soluzione](#source-code-is-included-in-debugging-solution)

2. [Non è possibile trovate il codice sorgente nella soluzione](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Il codice sorgente è incluso nella soluzione di debug

Il codice sorgente C# viene acquisito in due casi:

1. Il codice utente viene definito nel file code-behind (in genere denominato `Script.usql.cs` in un progetto U-SQL).

2. Il codice utente viene definito nel progetto di tipo libreria di classi C# per l'applicazione U-SQL e registrato come assembly con le **informazioni di debug**.

Se il codice sorgente viene importato nella soluzione, è possibile usare gli strumenti di debug di Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema:

1. Premere **F5** per avviare il debug. Il codice viene eseguito fino a quando non viene interrotto da un'eccezione.

2. Aprire il file del codice sorgente e impostare i punti di interruzione, quindi premere **F5** per eseguire nel dettaglio il debug del codice.

    ![Eccezione di debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Il codice sorgente non è incluso nella soluzione di debug

Se il codice utente non è incluso nel file code-behind o non si è registrato l'assembly con le **informazioni di debug**, il codice sorgente non viene incluso automaticamente nella soluzione di debug. In questo caso sono necessari passaggi aggiuntivi per aggiungere il codice sorgente:

1. Fare clic con il pulsante destro del mouse su **Soluzione 'VertexDebug' > Aggiungi > Progetto esistente** per trovare il codice sorgente dell'assembly e aggiungere il progetto alla soluzione di debug.

    ![Azure Data Lake Analytics U-SQL debug add project](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Ottenere il percorso della cartella del progetto **FailedVertexDebugHost**. 

3. Fare clic con il pulsante destro del mouse sul **progetto con codice sorgente assembly aggiunto > Proprietà**, selezionare la scheda **Compilazione** a sinistra e incollare il percorso copiato che termina con \bin\debug in **Output > Percorso output**. Il percorso di output finale è simile a "<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Percorso pdb impostato per il debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Dopo queste impostazioni, avviare il debug con **F5** e i punti di interruzione. È anche possibile usare gli strumenti di debug di Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema.

> [!NOTE]
> Ricreare il progetto di codice sorgente assembly dopo ogni modifica al codice per generare i file con estensione PDB aggiornati.

## <a name="resubmit-the-job"></a>Inviare di nuovo il processo

Dopo il debug, se il progetto viene completato correttamente la finestra di output mostra il messaggio seguente:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Debug U-SQL in Azure Data Lake Analytics completato correttamente](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Per inviare di nuovo il processo non riuscito:

1. Per i processi con soluzioni code-behind, copiare il codice C# nel file di origine code-behind, in genere `Script.usql.cs`.

2. Per i processi con assembly, fare clic con il pulsante destro del mouse sul progetto di codice sorgente degli assembly nella soluzione di debug e registrare gli assembly DLL aggiornati nel catalogo di Azure Data Lake.

3. Inviare nuovamente il processo U-SQL.

## <a name="next-steps"></a>Passaggi successivi

- [Guida di programmabilità di U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Sviluppare operatori U-SQL definiti dall'utente per i processi di Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Eseguire test e debug di processi U-SQL tramite l'esecuzione locale e l'SDK U-SQL di Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
- [Come risolvere i problemi relativi a un processo ricorrente anomalo](data-lake-analytics-data-lake-tools-debug-recurring-job.md)