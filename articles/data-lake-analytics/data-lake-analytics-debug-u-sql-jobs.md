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
   ms.date="09/02/2016"
   ms.author="jgao"/>



#Eseguire il debug del codice C# in U-SQL per processi di Data Lake Analytics 

Informazioni su come usare gli strumenti di Azure Data Lake Visual Studio per il debug di processi U-SQL non riusciti a causa di errori nel codice dell'utente.

Lo strumento Visual Studio consente di scaricare il codice compilato e i dati del vertice necessari dal cluster per tenere traccia ed eseguire il debug di processi non riusciti.

I sistemi di big data generalmente forniscono un modello di estendibilità tramite linguaggi come Java, C#, Python e così via. Molti di questi sistemi forniscono informazioni di debugging del runtime limitate, di conseguenza è difficile eseguire il debug degli errori di runtime nel codice personalizzato. Gli strumenti più recenti di Visual Studio sono dotati di una funzionalità di nome "Failed Vertex Debug". Grazie a questa funzionalità, è possibile scaricare i dati di runtime da Azure alla workstation locale in modo da eseguire il debug del codice C# che ha avuto esito negativo usando lo stesso runtime e gli stessi dati di input del cloud. Una volta risolti i problemi, è possibile rieseguire il codice rivisto in Azure dagli strumenti.

Per una presentazione video di questa funzionalità, vedere [Debug del codice personalizzato in Azure Data Lake Analytics](https://mix.office.com/watch/1bt17ibztohcb).

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

Se vengono usati operatori definiti dall'utente in U-SQL code-behind (Script.usql.cs), è necessario compilare un progetto Libreria di classi C# con il codice degli operatori definito dall'utente e includere il progetto nella soluzione VertexDebug.

Se sono stati registrati gli assembly con estensione dll nel database di Data Lake Analytics, è necessario aggiungere il codice sorgente degli assembly alla soluzione VertexDebug.
 
Se è stata creata una libreria di classi C# distinta per il codice U-SQL e sono state registrate assembly .dll nel database di Data Lake Analytics, è necessario aggiungere il progetto C# di origine degli assembly alla soluzione VertexDebug.

In alcuni rari casi, è possibile usare gli operatori definiti dall'utente nel codice U-SQL dietro il file (Script.usql.cs) nella soluzione originale. Affinché funzioni, è necessario creare una libreria C# contenente il codice sorgente e modificare il nome dell'assembly in quello registrato nel cluster. È possibile registrare il nome dell'assembly nel cluster selezionando lo script in esecuzione nel cluster. A tale scopo, aprire il processo U-SQL e fare clic su "script" nel pannello del processo.

**Per configurare la soluzione**

1.	Da Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto C# appena creato, quindi scegliere **Proprietà**.
2.	Impostare il percorso di output come percorso della directory di lavoro del progetto LocalVertexHost. È possibile ottenere il percorso della directory di lavoro del progetto LocalVertexHost tramite le proprietà LocalVertexHost.
3.	Compilare il progetto C# per inserire il file con estensione pdb nella directory di lavoro del progetto LocalVertexHost oppure copiare manualmente il file PDB in questa cartella.
4.	In **Exception Settings** (Impostazioni eccezioni) selezionare le eccezioni di Common Language Runtime:

![Impostazione visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##Eseguire il debug del processo

Dopo avere creato una soluzione di debug scaricando il vertice e avere configurato l'ambiente, è possibile avviare il debug del codice U-SQL.

1.	Da Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **LocalVertexHost** appena creato, puntare su **Debug** e quindi fare clic su **Avvia nuova istanza**. LocalVertexHost deve essere impostato come progetto di avvio. La prima volta è possibile che venga visualizzato il seguente messaggio, che è possibile ignorare. L'accesso alla schermata di debug può richiedere fino a un minuto.
 
    ![Avviso visual studio debug U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.	Usare l'esperienza di debug basata su Visual Studio, come espressioni di controllo, variabili e così via, per risolvere il problema.
5.	Dopo aver identificato un problema, correggere il codice e quindi ricompilare il progetto C# prima di eseguire di nuovo il test finché non vengono risolti tutti i problemi. Dopo che il debug è stato completato correttamente, la finestra di output indicherà il messaggio seguente

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##Inviare di nuovo il processo

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

<!---HONumber=AcomDC_0907_2016-->