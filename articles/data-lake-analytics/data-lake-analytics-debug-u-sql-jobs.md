<properties 
   pageTitle="Debug dei processi U-SQL | Microsoft Azure" 
   description="Informazioni su come eseguire il debug del vertice non riuscito di U-SQL con Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/26/2016"
   ms.author="jgao"/>



#Debug dei processi U-SQL 

Informazioni su come eseguire il debug di processi U-SQL non riusciti causati da errori nel codice dell'utente usando gli strumenti di Azure Data Lake Visual Studio. Lo strumento Data Lake Visual Studio consente agli utenti di scaricare il codice compilato e i dati del vertice necessari dal cluster per tenere traccia ed eseguire il debug di processi non riusciti.

>[AZURE.NOTE] Visual Studio potrebbe bloccarsi o arrestarsi in modo anomalo se non si dispone dei due aggiornamenti Windows seguenti: [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682) e [Universal C Runtime for Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##Prerequisiti
-	Avere esaminato con attenzione l'articolo di [introduzione](data-lake-analytics-data-lake-tools-get-started.md).

## Creare e configurare progetti di debug

Quando si apre un processo non riuscito nello strumento Data Lake di Visual Studio, verrà visualizzato un avviso. Le informazioni dettagliate sull'errore verranno visualizzate nella scheda relativa agli errori e nella barra di avviso gialla nella parte superiore della finestra.

![Vertice download visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Per scaricare vertex e creare una soluzione di debug**

1.	Aprire un processo di U-SQL non riuscito in Visual Studio.
2.	Fare clic su **Scarica** per scaricare tutte le risorse e i flussi di input necessari. Fare clic su **Riprova** in caso di errore del download.
3.	Fare clic su **Apri** al termine del download per creare un progetto di debug locale. Verrà creata una nuova soluzione di Visual Studio denominata **VertexDebug** con un progetto vuoto denominato **LocalVertexHost**.

Se vengono usati operatori definiti dall'utente in U-SQL code-behind (Script.usql.cs), è necessario compilare un progetto Libreria di classi C# con il codice degli operatori definito dall'utente e includere il progetto nella soluzione VertexDebug. Se sono stati registrati gli assembly con estensione dll nel database di Data Lake Analytics, è necessario aggiungere il codice sorgente degli assembly alla soluzione VertexDebug.
 

**Per configurare il progetto**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **LocalVertexHost** e quindi scegliere **Proprietà**.
2.	Impostare il percorso di output come percorso della directory di lavoro del progetto LocalVertexHost. È possibile ottenere il percorso della directory di lavoro del progetto LocalVertexHost tramite le proprietà LocalVertexHost.
3.	Compilare il progetto C# per inserire il file con estensione pdb nella directory di lavoro del progetto LocalVertexHost oppure copiare manualmente il file PDB in questa cartella.
4.	In **Exception Settings** (Impostazioni eccezioni) selezionare le eccezioni di Common Language Runtime:

![Impostazione visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##Eseguire il debug del processo

Dopo avere creato una soluzione di debug scaricando il vertice e avere configurato l'ambiente, è possibile avviare il debug del codice di U-SQL.

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **LocalVertexHost**, puntare su **Debug**, e quindi fare clic su **Avvia nuova istanza**. LocalVertexHost deve essere impostato come progetto di avvio. La prima volta è possibile che venga visualizzato il seguente messaggio, che è possibile ignorare. L'accesso alla schermata di debug può richiedere fino a un minuto.
 
    ![Avviso visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.	Usare l'esperienza di debug basata su Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema.
5.	Dopo aver identificato un problema, correggere il codice e quindi ricompilare il progetto C# prima di eseguire di nuovo il test finché non vengono risolti tutti i problemi. Dopo che il debug è stato completato correttamente, la finestra di output indicherà il messaggio seguente

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##per inviare di nuovo il processo

Dopo aver completato il debug del codice U-SQL, è possibile inviare nuovamente il processo non riuscito.

1. Registrare i nuovi assembly con estensione dll per il database ADLA.

    1.	Da Esplora server e Cloud Explorer nello strumento Data Lake di Visual Studio espandere il nodo **database**
    2.	Fare clic con il pulsante destro del mouse sull'opzione per registrare gli assembly.
    3.	Registrare i nuovi assembly con estensione dll per il database ADLA.
 
2.	Oppure copiare il codice C# nel file script.usql.cs - c# code-behind.
3.	Inviare di nuovo il processo.

##Passaggi successivi

- [Esercitazione: Introduzione al linguaggio U-SQL di Analisi Data Lake di Azure](data-lake-analytics-u-sql-get-started.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Sviluppare operatori U-SQL definiti dall'utente per i processi di Analisi Azure Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0727_2016-->