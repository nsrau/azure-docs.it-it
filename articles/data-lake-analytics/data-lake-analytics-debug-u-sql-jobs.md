---
title: Debug dei processi U-SQL | Documentazione Microsoft
description: Informazioni su come eseguire il debug del vertice non riuscito di U-SQL con Visual Studio.
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Eseguire il debug del codice C# definito dall'utente per i processi U-SQL non riusciti

U-SQL offre un modello estendibilità che usa C#, pertanto è possibile scrivere il codice per aggiungere funzionalità quali un estrattore o riduttore personalizzato. Per altre informazioni vedere [Guida alla programmabilità di U-SQL](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). In pratica un codice per cui potrebbe essere necessario il debug e i sistemi di dati di grandi dimensioni possono offrire solo informazioni sul debug del runtime quali i file di log.

Strumenti Azure Data Lake per Visual Studio offre una funzionalità denominata **Debug del vertice con errore**, che consente di clonare un processo non riuscito dal cloud nel computer locale per il debug. Il clone locale acquisisce tutto l'ambiente cloud, inclusi eventuali dati di input e il codice utente.

Nel video seguente viene illustrata la funzione Debug del vertice con errore in Strumenti Azure Data Lake per Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Se non sono già installati Visual Studio richiede i due aggiornamenti seguenti: [Aggiornamento di Microsoft Visual C++ 2015 Redistributable 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) e [Universal C Runtime per Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Scaricare il vertice con errori nel computer locale

Quando si apre un processo non riuscito in Strumenti Azure Data Lake per Visual Studio, viene visualizzata una barra di avviso gialla con messaggi di errore dettagliati nella scheda errore.

1. Fare clic su **Scarica** per scaricare tutte le risorse e i flussi di input necessari. Se il download non viene completato, fare clic su **Riprova**.

2. Fare clic su **Apri** dopo aver completato il download per generare l'ambiente di debug locale. Viene creata e aperta automaticamente una nuova istanza di Visual Studio con una soluzione di debug.

![Vertice download visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

I processi possono includere i file di origine code-behind o assembly registrati e questi due tipi presentano diversi scenari di debug.

- [Eseguire il debug di un processo non riuscito con code-behind](#debug-job-failed-with-code-behind)
- [Eseguire il debug di un processo non riuscito con assembly](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Debug di un processo con errori con code-behind

Se un processo U-SQL non riesce e include il codice utente, in genere denominato `Script.usql.cs` in un progetto U-SQL, il codice sorgente viene importato nella soluzione di debug.  Da qui è possibile usare gli strumenti di debug di Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema.

> [!NOTE]
> Prima del debug, assicurarsi di selezionare le **Common Language Runtime Exceptions** (Eccezioni di Common Language Runtime) nella finestra Impostazioni eccezioni (**CTRL + ALT + E**).

![Impostazione visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Premere **F5** per eseguire il codice code-behind, che verrà eseguito fino a quando non viene interrotto da un'eccezione.

2. Aprire il file `ADLTool_Codebehind.usql.cs` e impostare i punti di interruzione, quindi premere **F5** per eseguire nel dettaglio il debug del codice.

    ![Eccezione di debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Debug di un processo con errori con assembly

Se si usano gli assembly registrati nello script U-SQL, il sistema non riesce a recuperare automaticamente il codice sorgente. In questo caso, aggiungere manualmente il file di codice sorgente degli assembly alla soluzione.

### <a name="configure-the-solution"></a>Configurare la soluzione

1. Fare clic con il pulsante destro del mouse su **Solution 'VertexDebug' (Soluzione "VertexDebug") > Aggiungi > Progetto esistente...** per individuare il codice sorgente degli assembly e aggiungere il progetto alla soluzione di debug.

    ![Azure Data Lake Analytics U-SQL debug add project](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Fare clic con il pulsante destro del mouse su **LocalVertexHost > Proprietà** nella soluzione, quindi copiare il percorso **Directory di lavoro**.

3. Fare clic con il pulsante destro del mouse sul **progetto con codice sorgente assembly > Proprietà**, selezionare la scheda **Genera** a sinistra e incollare il percorso copiato in **Output > Percorso output**.

    ![Percorso pdb impostato per il debug U-SQL in Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Premere **CTRL + ALT + E**, controllare le **eccezioni di Common Language Runtime** nella finestra Impostazioni eccezioni.

### <a name="start-debug"></a>Avviare il debug

1. Fare clic con il tasto destro del mouse sul **progetto con codice sorgente assembly > Ricrea** per inviare i file con estensione PDB alla directory di lavoro `LocalVertexHost`.

2. Premere **F5**. Il progetto verrà eseguito fino a quando non sarà interrotto da un'eccezione. È possibile che venga visualizzato il messaggio di avviso seguente, che è possibile ignorare. L'accesso alla schermata di debug può richiedere fino a un minuto.

    ![Avviso visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Aprire il codice sorgente e impostare i punti di interruzione, quindi premere **F5** per eseguire nel dettaglio il debug del codice.

È anche possibile usare gli strumenti di debug di Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema.

> [!NOTE]
> Ricreare il progetto di codice sorgente assembly dopo ogni modifica al codice per generare i file con estensione PDB aggiornati.

Dopo il debug, se il progetto viene completato correttamente la finestra di output mostra il messaggio seguente:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Debug U-SQL in Azure Data Lake Analytics completato correttamente](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Inviare di nuovo il processo

Dopo aver completato il debug è possibile inviare nuovamente il processo non riuscito.

1. Per i processi con soluzioni code-behind, copiare il codice C# nel file di origine code-behind, in genere `Script.usql.cs`.
2. Per i processi con gli assembly, registrare gli assembly DLL aggiornati nel database ADLA:
    1. In Esplora server o Cloud Explorer espandere il nodo **Account ADLA > Database**.
    2. Fare clic con il pulsante destro del mouse su **Assembly** e registrare i nuovi assembly DLL con il database ADLA: ![assembly di debug U-SQL di Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Inviare di nuovo il processo.

## <a name="next-steps"></a>Passaggi successivi

- [Guida di programmabilità di U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Sviluppare operatori U-SQL definiti dall'utente per i processi di Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
