<properties linkid="manage-services-hdinsight-recommendation-engine-using-mahout" urlDisplayName="Hadoop Recommendation Engine" pageTitle="Hadoop recommendation engine (.NET) | Azure" metaKeywords="Azure Apache Mahout, Azure recommendation example, Azure recommendation tutorial, Azure recommendation engine" description="A tutorial that teaches how to use the Apache Mahout recommendation engine with Azure to create song suggestions based on listening habits." disqusComments="1" umbracoNaviHide="1" title="Simple recommendation engine using Apache Mahout" authors="jgao" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Utilizzo di Apache Mahout per un motore di raccomandazione semplice

Apache Mahout™ è una libreria di Machine Learning destinata all'utilizzo in applicazioni di Machine Learning scalabili. I motori di raccomandazione come Mahout sono uno dei tipi più diffusi di applicazioni di Machine Learning attualmente in uso e includono numerose applicazioni di marketing ovvie.

Apache Mahout fornisce un'implementazione integrata per il filtro collaborativo basato su elementi. Questo approccio è ampiamente utilizzato per eseguire il data mining delle raccomandazioni. Sviluppato da Amazon.com, il filtro collaborativo basato su elementi si basa sul principio in base al quale i dati sulle preferenze degli utenti che mostrano correlazioni tra le preferenze per determinati elementi possono essere utilizzati per dedurre i gusti di futuri utenti di un gruppo simile.

In questa esercitazione si utilizzerà il sito [Million Song Dataset][] e si scaricherà il set di dati per creare l'elenco delle canzoni consigliate sulla base delle canzoni ascoltate in passato dagli utenti.

Si apprenderà come:

-   Utilizzare i motori di raccomandazione

Questa esercitazione è suddivisa nelle sezioni seguenti:

1.  [Installazione e configurazione][]
2.  [Esame e formattazione dei dati][]
3.  [Installazione di Mahout][]
4.  [Esecuzione del processo Mahout][]

## <a name="setup"></a>Installazione e configurazione

Per questa esercitazione si presuppone che siano già stati installati Azure e l'anteprima di HDInsight e che sia stato creato un HDInsight in cui è possibile eseguire un esempio. Se non è già stato fatto, consultare l'esercitazione [Introduzione ad Azure HDInsight][] per istruzioni su come soddisfare questi prerequisiti.

## <a name="segment1"></a>Esame e formattazione dei dati

In questo esempio viene spiegato in che modo gli utenti esprimono una preferenza per una certa canzone. Il presupposto è che la preferenza di un utente per una canzone è data dal numero di volte in cui un utente ascolta una canzone. I modelli rilevati nei dati sulle preferenze possono essere utilizzati per prevedere le preferenze future degli utenti sulla base di alcune preferenze musicali espresse. Per un esempio di questo set di dati, vedere la sezione relativa alla **descrizione** della pagina Web su [Echo Nest Taste Profile Subset][Million Song Dataset]:

![Subset Taste Profile di Echo Nest][]

### Dati di esempio del set di dati Million Song

Per utilizzare questo set di dati con Mahout, è necessario effettuare due operazioni:

1.  Convertire gli ID delle canzoni e degli utenti in valori interi.
2.  Salvare i nuovi valori con le relative classificazioni in un file con valori separati da virgole.

Se Visual Studio 2010 non è installato, ignorare questo passaggio e procedere con la sezione relativa all'esecuzione di un processo Mahout per ottenere una versione già generata.

Per iniziare, avviare Visual Studio 2010. In Visual Studio selezionare **File -\> Nuovo -\> Progetto**. Nel riquadro **Modelli installati** espandere il nodo **Visual C#**, selezionare la categoria **Windows**, quindi selezionare **Applicazione console** nell'elenco. Assegnare al progetto il nome "ConvertToMahoutInput" e fare clic su **OK**.

![Creazione di un'applicazione console][]

### Creazione di un'applicazione console

1.  Dopo aver creato l'applicazione, aprire il file **Program.cs** e aggiungere i seguenti membri static alla classe **Program**:

        const char tab = '\u0009';
        static Dictionary<string, int> usersMapping = new Dictionary<string, int>();
        static Dictionary<string, int> songMapping = new Dictionary<string, int>(); 

2.  Aggiungere quindi l'istruzione `using System.IO;` e compilare il metodo **Main** con il codice seguente:

        var inputStream = File.Open(args[0], FileMode.Open);
        var reader = new StreamReader(inputStream);

        var outStream = File.Open("mInput.txt", FileMode.OpenOrCreate);
        var writer = new StreamWriter(outStream);

        var i = 1;

        var line = reader.ReadLine();
        while (!string.IsNullOrWhiteSpace(line))
        {
            i++;
            if (i > 5000)
            break;
            var outLine = line.Split(tab);

            int user = GetUser(outLine[0]);
            int song = GetSong(outLine[1]);

            writer.Write(user);
            writer.Write(',');
            writer.Write(song);
            writer.Write(',');
            writer.WriteLine(outLine[2]);

            line = reader.ReadLine();
        }

        Console.WriteLine("saved {0} lines to {1}", i, args[0]);

        reader.Close();
        writer.Close();

        SaveMapping(usersMapping, "users.csv");
        SaveMapping(songMapping, "mInput.csv");

        Console.WriteLine("Mapping saved");
        Console.ReadKey();

3.  A questo punto creare le funzioni **GetUser** e **GetSong** che convertono gli ID in interi:

        static int GetUser(string user)
        {
            if (!usersMapping.ContainsKey(user))
                usersMapping.Add(user, usersMapping.Count + 1);

            return usersMapping[user];
        }

        static int GetSong(string song)
        {
            if (!songMapping.ContainsKey(song))
                songMapping.Add(song, songMapping.Count + 1);

            return songMapping[song];
        }

4.  Creare infine l'utilità che implementa il metodo SaveMapping per salvare i dizionari di mapping in file con estensione csv.

        static void SaveMapping(Dictionary<string, int> mapping, string fileName)
        {
            var stream = File.Open(fileName, FileMode.Create);
            var writer = new StreamWriter(stream);

            foreach (var key in mapping.Keys)
            {
                writer.Write(key);
                writer.Write(',');
                writer.WriteLine(mapping[key]);
            }

            writer.Close();
        }

5.  Scaricare i dati di esempio da [questo collegamento][]. Una volta scaricati, aprire **train\_triplets.txt.zip** ed estrarre **train\_triplets.txt**.

    Durante l'esecuzione dell'utilità, includere un argomento della riga di comando con il percorso di **train\_triplets.txt**. A tale scopo, fare clic con il pulsante destro del mouse sul nodo del progetto **ConvertToMahoutInput** in **Esplora soluzioni** e scegliere **Proprietà**. Nella pagina delle proprietà del progetto selezionare la scheda **Debug** sul lato sinistro e aggiungere il percorso di \<percorsolocale\>train\_triplets.txt nella casella di testo **Argomenti della riga di comando**:

    ![Impostazione degli argomenti della riga di comando][]

### Impostazione degli argomenti della riga di comando

-   Premere **F5** per eseguire il programma. Al termine, aprire la cartella **bin\\Debug** dal percorso in cui è stato salvato il progetto e visualizzare l'output dell'utilità. Dovrebbero essere presenti i file users.txt e mInput.txt.

## <a name="segment2"></a>Installazione di Mahout

-   Aprire il portale cluster HDInsight e fare clic sull'icona **Remote Desktop**.

    ![Icona Manage Cluster][]

### Icona Remote Desktop

Per impostazione predefinita, HDInsight non include Mahout, tuttavia poiché fa parte dell'ecosistema di Hadoop, può essere scaricato dal sito Web [Mahout][]. La versione più recente è la 0.7, ma questo set di istruzioni è compatibile anche per la versione 0.5.

1.  Scaricare in primo luogo [Mahout versione 0.7][] nel computer locale.

2.  Copiarlo nel cluster selezionando il file ZIP locale e premendo CTRL+C, quindi premere CTRL+V per incollarlo nel cluster Hadoop.

    ![Caricamento di Mahout][]

### Copia di Mahout nel nodo head

1.  Fare infine clic con il pulsante destro del mouse al termine del processo di copia ed estrarre la distribuzione Mahout in C:\\apps\\dist. Mahout verrà installato in C:\\apps\\dist\\mahout-distribution-0.7.

2.  Per semplicità rinominare la cartella in c:\\apps\\dist\\mahout-0.7.

### <a name="segment3"></a>Esecuzione del processo Mahout

1.  Copiare il file **mInput.txt** dalla cartella **bin\\Debug** in **c:\\** nel cluster remoto. Una volta copiato il file, estrarlo. Come menzionato nella sezione precedente, per copiare un file nella sessione RDP remota, è sufficiente premere CTRL+C nel computer locale dopo aver selezionato i file e quindi premere CTRL+V nella finestra della sessione RDP.

2.  Creare un file che contiene l'ID dell'utente per cui verranno generate le canzoni consigliate. A tale scopo, creare in **c:\\** un file di testo denominato **users.txt** contenente l'ID di un singolo utente.

<div class="dev-callout"> 
<b>Nota</b> 
<p>&Egrave; possibile generare le canzoni consigliate per altri utenti inserendo i relativi ID su righe distinte. In caso di problemi durante la generazione dei file mInput.txt e users.txt &egrave; possibile scaricare una versione generata in precedenza da questo <a href="https://github.com/wenming/BigDataSamples/tree/master/mahout">archivio</a> GitHub. 

La soluzione pi&ugrave; comoda consiste nello scaricare tutto come unico <a href="https://github.com/wenming/BigDataSamples/archive/master.zip">file ZIP</a>. Individuare i file users.txt e mInput.txt e copiarli nella cartella c:\ del nel cluster remoto.</p> 
</div>

A questo punto è necessario aprire una finestra del terminale di Hadoop e passare alla cartella che contiene i file users.txt e mInput.txt.

![Finestra dei comandi di Mahout][]

### Finestra dei comandi di Hadoop

1.  Copiare quindi sia **mInput.txt** che **users.txt** in HDFS. A tale scopo, aprire la **shell dei comandi di Hadoop** ed eseguire i comandi seguenti:

        hadoop dfs -copyFromLocal c:\mInput.txt input\mInput.txt
        hadoop dfs -copyFromLocal c:\users.txt input\users.txt

2.  Verificare che i file siano stati copiati in HDFS:

        hadoop fs -ls input/

    Verrà ora visualizzato quanto segue:

        Found 2 items
        -rwxrwxrwx   1 writer supergroup      53322 2013-03-08 20:32 /user/writer/input/mInput.txt
        -rwxrwxrwx   1 writer supergroup        353 2013-03-08 20:33 /user/writer/input/users.txt

3.  A questo punto è possibile eseguire il processo Mahout con il comando seguente:

        c:\apps\dist\mahout-0.7\bin>hadoop jar c:\Apps\dist\mahout-0.7\mahout-core-0.7-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input=input/mInput.txt --output=output --usersFile=input/users.txt

    Sono disponibili numerose altre funzioni di "distanza" utilizzabili dal motore di raccomandazione per confrontare il fattore delle funzionalità per utenti diversi. È possibile sperimentare e modificare la classe Similarity impostandola su SIMILARITY\_COOCCURRENCE, SIMILARITY\_LOGLIKELIHOOD, SIMILARITY\_TANIMOTO\_COEFFICIENT, SIMILARITY\_CITY\_BLOCK, SIMILARITY\_COSINE, SIMILARITY\_PEARSON\_CORRELATION, SIMILARITY\_EUCLIDEAN\_DISTANCE. Ai fini di questa esercitazione non verranno approfonditi gli aspetti correlati alla scienza dei dati di Mahout.

4.  L'esecuzione del processo Mahout richiederà diversi minuti, dopo i quali verrà creato un file di output. Eseguire il comando seguente per creare una copia locale del file di output:

        hadoop fs -copyToLocal output/part-r-00000 c:\output.txt

5.  Aprire il file **output.txt** dalla cartella radice **c:\\** ed ispezionarne il contenuto. La struttura del file è la seguente:

        user    [song:rating,song:rating, ...]

6.  Se si desidera utilizzare altre parti di Mahout nel cluster, è consigliabile salvare una copia di Mahout.cmd nella directory bin della distribuzione di Mahout.

## <a name="summary"></a>Riepilogo

I motori di raccomandazione offrono importanti funzionalità per molti moderni siti di social networking, acquisti online, flussi multimediali e altri siti Internet. In Mahout è disponibile un motore di raccomandazione già pronto e facile da utilizzare che contiene molte utili funzionalità ed è scalabile in Hadoop.

## Passaggi successivi

In questo articolo è stato illustrato l'utilizzo della riga comandi di Hadoop, tuttavia è possibile eseguire attività anche tramite la console interattiva di HDInsight. Per ulteriori informazioni, vedere [Linee guida: console JavaScript e Hive interattive di HDInsight][interactive-console].

  [Million Song Dataset]: http://labrosa.ee.columbia.edu/millionsong/tasteprofile
  [Installazione e configurazione]: #setup
  [Esame e formattazione dei dati]: #segment1
  [Installazione di Mahout]: #Segment2
  [Esecuzione del processo Mahout]: #segment2
  [Introduzione ad Azure HDInsight]: /en-us/manage/services/hdinsight/get-started-hdinsight/
  [Subset Taste Profile di Echo Nest]: ./media/hdinsight-hadoop-recommendation-engine/the-echo-nest-taste-profile-subset.png
  [Creazione di un'applicazione console]: ./media/hdinsight-hadoop-recommendation-engine/creating-a-console-application.png
  [questo collegamento]: http://labrosa.ee.columbia.edu/millionsong/sites/default/files/challenge/train_triplets.txt.zip
  [Impostazione degli argomenti della riga di comando]: ./media/hdinsight-hadoop-recommendation-engine/setting-command-line-arguments.png
  [Icona Manage Cluster]: ./media/hdinsight-hadoop-recommendation-engine/the-manage-cluster-icon.png
  [Mahout]: http://mahout.apache.org/
  [Mahout versione 0.7]: http://www.apache.org/dyn/closer.cgi/mahout/
  [Caricamento di Mahout]: ./media/hdinsight-hadoop-recommendation-engine/uploading-mahout.PNG
  [archivio]: https://github.com/wenming/BigDataSamples/tree/master/mahout
  [file ZIP]: https://github.com/wenming/BigDataSamples/archive/master.zip
  [Finestra dei comandi di Mahout]: ./media/hdinsight-hadoop-recommendation-engine/mahout-commandwindow.PNG
