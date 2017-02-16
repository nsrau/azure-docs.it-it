---
title: Debug dei processi U-SQL | Documentazione Microsoft
description: 'Informazioni su come eseguire il debug del vertice non riuscito di U-SQL con Visual Studio. '
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
ms.date: 09/02/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: a5bb452582f05981a17c2514e0e40db0571bf61d
ms.openlocfilehash: f9b485bfbfbeb8a95ae1908ef6b1733b9cc6999a


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Eseguire il debug di codice C# definito dall'utente per i processi U-SQL non riusciti

Informazioni su come eseguire il debug di processi U-SQL non riusciti con bug nel codice definito dall'utente usando Strumenti Azure Data Lake per Visual Studio.

## <a name="background"></a>Background

U-SQL fornisce il modello di estensibilità tramite C#. Gli utenti possono pertanto scrivere codice C# definito dall'utente, come estrattori e riduttori, per ottenere maggiore estendibilità (ulteriori informazioni sul [Codice U-SQL definito dall'utente](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)). Tuttavia, nessuno sviluppatore può scrivere codice senza il minimo errore e, al contempo, il debug nei sistemi Big Data è complesso poiché molti sistemi forniscono solo informazioni di debug del runtime limitate come log e così via. 

Strumenti Azure Data Lake per Visual Studio offre una funzionalità denominata **Debug del vertice con errore**. Con questa è possibile clonare con facilità dal cloud al computer locale gli ambienti con errori, inclusi ad esempio dati di input e codici utenti intermedi. L'utente può a quel punto individuare ed eseguire il debug dei job con errori esattamente con lo stesso runtime e gli stessi dati di input del cloud.

Nel video seguente viene illustrata la funzione **Debug del vertice con errore** in Strumenti Azure Data Lake per Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>
>

> [!NOTE]
> Visual Studio potrebbe bloccarsi o arrestarsi in modo anomalo se non si dispone dei due aggiornamenti Windows seguenti: [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) e [Universal C Runtime for Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* Avere esaminato con attenzione l'articolo di [introduzione](data-lake-analytics-data-lake-tools-get-started.md) .

## <a name="download-failed-vertex-to-local"></a>Scaricare il vertice con errori in locale

Quando si apre un processo con errori in Strumenti Azure Data Lake per Visual Studio, si riceverà una notifica. I messaggi dettagliati sull'errore verranno visualizzate nella scheda relativa agli errori e nella barra di avviso gialla nella parte superiore della finestra.

1. Fare clic su **Scarica** per scaricare tutte le risorse e i flussi di input necessari. Fare clic su **Riprova** in caso di errore del download. 

2. Fare clic su **Apri** una volta completato il download per generare l'ambiente di debug locale. Verrà creata e aperta automaticamente una nuova istanza di Visual Studio con una soluzione di debug. 

3. Le fasi del debug variano leggermente tra i processi con code-behind e i processi con assembly che presentano errori.

    - [Debug di un processo con errori con code-behind](#debug-job-failed-with-code-behind)
    - [Debug di un processo con errori con assembly](#debug-job-failed-with-assemblies)

![Vertice download visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>Debug di un processo con errori con code-behind

Se si scrive codice definito dall'utente nel file code-behind (in genere denominato "Script.usql.cs" nel progetto U-SQL) e il processo U-SQL presenta successivamente degli errori, il codice sorgente verrà importato automaticamente nella soluzione di debug degenerato. A quel punto è possibile usare semplicemente l'esperienza di debug basata su Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema. 

Prima del debug, assicurarsi di verificare le **eccezioni di Common Language Runtime** nella finestra Impostazioni eccezioni (**CTRL + ALT + E**).

![Impostazione visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Premere **F5**. Il codice code-behind verrà eseguito automaticamente fino a quando non sarà interrotto da un'eccezione.

2. Aprire **ADLTool_Codebehind.usql.cs** nel progetto, impostare i punti di interruzione e premere F5 per eseguire nel dettaglio il debug del codice.

    ![Eccezione di debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Debug di un processo con errori con assembly

Se si usano assembly registrati nello script U-SQL, il sistema non sarà in grado di ottenere automaticamente il codice sorgente. Sarà quindi necessario effettuare alcune configurazioni prima di eseguire il debug del codice definito dall'utente. Più nello specifico, si dovrà aggiungere il codice sorgente degli assembly alla soluzione generata automaticamente.

### <a name="configure-the-solution"></a>Configurare la soluzione

1. Fare clic con il tasto destro del mouse su **Solution 'VertexDebug' (Soluzione "VertexDebug")** > **Aggiungi** > **Progetto esistente...** per individuare il codice sorgente degli assembly e aggiungere il progetto alla soluzione di debug.


    ![Aggiunta progetto di debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Fare clic con il tasto destro del mouse su **LocalVertexHost** > **Proprietà** nella soluzione, quindi copiare il percorso copiare **Directory di lavoro**.

3. Fare clic con il tasto destro del mouse sul **progetto con codice sorgente assembly** > **Proprietà**, selezionare la scheda **Genera** a sinistra e incollare il percorso copiato in 2 in **Output** > **Percorso output**.  

    ![Percorso pdb impostato per il debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Premere **CTRL + ALT + E**, controllare le **eccezioni di Common Language Runtime** nella finestra Impostazioni eccezioni.

### <a name="start-debug"></a>Avviare il debug

1. Fare clic con il tasto destro del mouse sul **progetto con codice sorgente assembly** > **Ricompila** per inviare i file .PDB alla directory di lavoro LocalVertexHost.

2. Premere **F5**. Il progetto verrà eseguito automaticamente fino a quando non sarà interrotto da un'eccezione. La prima volta è possibile che venga visualizzato il seguente messaggio, che è possibile ignorare. L'accesso alla schermata di debug può richiedere fino a un minuto.

    ![Avviso visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Aprire il codice sorgente e impostare i punti di interruzione, quindi premere **F5** per eseguire nel dettaglio il debug del codice.

È anche possibile usare altre esperienze di debug basate su Visual Studio, come espressioni di controllo, variabili e così via, per eseguire il debug del problema. 

**Si noti che** è necessario ricompilare il progetto di codice sorgente assembly ogni volta che si modifica il codice per rendere effettivi i nuovi file pdb.

Dopo che il debug è stato completato correttamente, la finestra di output indicherà il messaggio seguente:

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Debug U-SQL in Azure Data Lake Analytics completato correttamente](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Inviare di nuovo il processo
Dopo aver completato il debug è possibile inviare nuovamente il processo non riuscito.

1. Registrare i nuovi assembly con estensione dll per il database ADLA.

    1. Da Esplora server/Cloud Explorer espandere il nodo **Account ADLA** > **Database**.
    2. Fare clic con il pulsante destro del mouse su **Assembly** per registare gli assembly. 
    3. Registrare i nuovi assembly con estensione dll per il database ADLA.
    ![Registrazione assembly nel debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. Oppure copiare nuovamente il codice C# nel file script.usql.cs - C# code-behind.
3. Inviare di nuovo il processo.

## <a name="next-steps"></a>Passaggi successivi

* [Guida di programmabilità di U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
* [Sviluppare operatori U-SQL definiti dall'utente per i processi di Analisi Azure Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [Esercitazione: Introduzione al linguaggio U-SQL di Analisi Data Lake di Azure](data-lake-analytics-u-sql-get-started.md)
* [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)





<!--HONumber=Jan17_HO1-->


