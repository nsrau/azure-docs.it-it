<properties
   pageTitle="Sviluppo di processi Python MapReduce con HDInsight | Microsoft Azure"
   description="Informazioni su come creare ed eseguire processi Python MapReduce nei cluster HDInsight basati su Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/27/2016"
   ms.author="larryfr"/>

#Sviluppo di programmi per la creazione di flussi Python per HDInsight

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. In questo articolo si apprenderà come usare Python per eseguire operazioni MapReduce.

> [AZURE.NOTE] Mentre il codice Python in questo documento può essere utilizzato con un cluster HDInsight basato su Windows, i passaggi descritti in questo documento sono specifici di cluster basati su Linux.

Questo articolo si basa su informazioni ed esempi pubblicati da Michael Noll nell'articolo relativo alla [scrittura di un programma MapReduce di Hadoop in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

##Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop basato su Linux in HDInsight

* Un editor di testo

    > [AZURE.IMPORTANT] L'editor di testo deve usare LF come terminazione di riga. Se usa CRLF, ciò causerà errori in seguito all'esecuzione del processo MapReduce su cluster HDInsight basati su Linux. In caso di dubbi, usare il passaggio facoltativo nella sezione [Eseguire MapReduce](#run-mapreduce) per convertire ogni CRLF in LF.

* Per i client Windows, PuTTY e PSCP, Queste utilità sono disponibili nella <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">pagina di download PuTTY</a>.

##Conteggio di parole

Per questo esempio, si implementerà un conteggio di parole di base usando un mapper e un reducer. Il mapper scompone le frasi in singole parole e il reducer aggrega le parole e i conteggi per produrre l'output.

Il diagramma di flusso illustra ciò che accade durante le fasi di mapping e riduzione.

![illustrazione di MapReduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Perché Python?

Python è un linguaggio di programmazione generale di alto livello che consente di esprimere concetti in un minor numero di righe di codice rispetto a molti altri linguaggi. Si è recentemente diffuso presso i data scientist come linguaggio per la creazione di prototipi grazie alla natura interpretata, alla tipizzazione dinamica e alla sintassi elegante che lo rendono appropriato per lo sviluppo rapido di applicazioni.

Python è installato in tutti i cluster HDInsight.

##Flusso di MapReduce

Hadoop consente di specificare un file che contiene la logica di mapping e riduzione usata da un processo. I requisiti specifici per la logica di mapping e riduzione sono:

* **Input**: i componenti di mapping e riduzione devono leggere i dati di input da STDIN.

* **Output**: i componenti di mapping e riduzione devono scrivere i dati di output in STDOUT.

* **Formato dati**: i dati usati e prodotti devono essere una coppia chiave/valore, separati da un carattere di tabulazione.

Python è in grado di gestire facilmente questi requisiti usando il modulo **sys** per leggere da STDIN e usando **print** per stampare in STDOUT. Il resto dell'attività consiste semplicemente nella formattazione dei dati con un carattere di tabulazione (`\t`) tra la chiave e il valore.

##Creare il mapper e il reducer

Il mapper e il reducer sono file di testo, in questo caso **mapper.py** e **reducer.py** (per indicare chiaramente le operazioni eseguite da ciascuno). È possibile crearli usando l'editor di propria scelta.

###Mapper.py

Creare un nuovo file denominato **mapper.py** e usare il seguente codice come contenuto:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Soffermarsi per leggere il codice e comprenderne la funzione.

###Reducer.py

Creare un nuovo file denominato **reducer.py** e usare il seguente codice come contenuto:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##Caricare i file

Sia **mapper.py** sia **reducer.py** devono essere contenuti nel nodo head del cluster perché sia possibile eseguirli. Il modo più semplice per caricarli consiste nell'usare **scp** (**pscp** con un client Windows).

Dal client, usare il seguente comando dalla stessa directory di **mapper.py** e **reducer.py**. Sostituire **username** con un nome utente SSH e **clustername** con il nome del cluster.

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

In questo modo i file verranno copiati dal sistema locale nel nodo head.

> [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata, ad esempio `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##Eseguire MapReduce

1. Connettersi al cluster usando SSH:

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE] Se è stata usata una password per proteggere l'account SSH, verrà richiesto di specificarla. Se è stata usata una chiave SSH, potrebbe essere necessario usare il parametro `-i` e il percorso della chiave privata, ad esempio `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Facoltativo) Se, quando si creano i file mapper.py e reducer.py, si usa un editor di testo che inserisce CRLF come terminazione di riga oppure non si sa quale terminazione di riga verrà usata dall'editor, convertire le occorrenze di CRLF di mapper.py e reducer.py in LF mediante i seguenti comandi.

        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py

2. Usare il seguente comando per avviare il processo MapReduce.

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout

	Questo comando include le seguenti parti:

	* **hadoop-streaming.jar**: usato durante l'esecuzione di operazioni di flusso MapReduce. Consente ad Hadoop di interagire con il codice MapReduce esterno fornito dall'utente.

	* **-files**: indica ad Hadoop che i file specificati sono necessari per il processo MapReduce e devono essere copiati in tutti i nodi di lavoro.

	* **-mapper**: indica ad Hadoop quale file usare come mapper.

	* **-reducer**: indica ad Hadoop quale file usare come reducer.

	* **-input**: il file di input di cui devono essere contate le parole.

	* **-output**: la directory in cui verrà scritto l'output.

		> [AZURE.NOTE] Questa directory verrà creata dal processo.

Si noteranno alcune istruzioni **INFO** all'avvio del processo e infine le operazioni **map** e **reduce** visualizzate come valori percentuali.

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Verranno visualizzate informazioni sullo stato del processo quando verrà completato.

##Visualizzare l'output

Una volta completato il processo, usare il seguente comando per visualizzare l'output:

	hdfs dfs -text /example/wordcountout/part-00000

Verrà visualizzato un elenco di parole con l'indicazione del numero di occorrenze di ciascuna. Di seguito è riportato un esempio di dati di output:

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##Passaggi successivi

Dopo aver appreso come usare i processi di flusso MapReduce con HDInsight, vedere i seguenti collegamenti per esplorare altri modi di uso di Azure HDInsight.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->