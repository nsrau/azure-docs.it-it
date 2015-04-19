<properties
   pageTitle="Usare Pig con Hadoop in HDInsight | Azure"
   description="Informazioni su come usare Pig con Hadoop in HDInsight mediante SSH."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>
 
#Eseguire processi Pig usando il comando Pig (SSH)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Questo documento illustra in dettaglio il processo di connessione a un cluster HDInsight di Azure basato su Linux usando Secure Shell (SSH), quindi usando il comando Pig per eseguire istruzioni Pig Latin in modo interattivo o come processo batch.

Il linguaggio di programmazione Pig Latin consente di descrivere le trasformazioni applicate ai dati di input per produrre l'output desiderato.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere <a href="../hdinsight-hadoop-linux-information/" target="_blank">informazioni utili su Hadoop in HDInsight basato su Linux</a>.

##<a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight basato su Linux (Hadoop su HDInsight).

* Un client SSH. Linux, Unix e Mac OS dovrebbero essere dotati di un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Connettersi tramite SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

###Putty (client basati su Windows)

Windows non fornisce un client SSH incorporato. È consigliabile usare **Putty**, disponibile per il download all'indirizzo <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

Per altre informazioni sull'uso di Putty, vedere la sezione sull'**uso di Putty per la connessione a un computer Linux** nella pagina relativa all' <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">uso di SSH con Linux in Azure</a>.

> [AZURE.NOTE] Se è stato usato un certificato per l'autenticazione SSH per il cluster HDInsight, sarà necessario anche leggere la sezione sulla **creazione di una chiave privata PPK per Putty** nella pagina relativa all'<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">uso di SSH con Linux in Azure</a>

##<a id="pig"></a>Usare il comando Pig

2. Una volta connessi, avviare l'interfaccia della riga di comando di Pig (CLI) mediante il comando seguente.

        pig

	Dopo qualche istante verrà visualizzato un `grunt>` prompt dei comandi.

3. Immettere la seguente istruzione.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Questo comando carica i contenuti del file sample. log in LOGS. È possibile visualizzare i contenuti del file usando il seguente comando.

		DUMP LOGS;

4. Successivamente, trasformare i dati applicando un'espressione regolare per estrarre solo il livello di registrazione da ogni record usando il comando seguente.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	È possibile usare **DUMP** per visualizzare i dati dopo la trasformazione. In questo caso, usare `DUMP LEVELS;`.

5. Continuare ad applicare le trasformazioni usando le seguenti istruzioni. Usare `DUMP` per visualizzare il risultato della trasformazione dopo ogni passaggio.

	<table>
	<tr>
	<th>Istruzione</th><th>Risultato</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Rimuove le righe che contengono un valore null per il livello di registrazione e memorizza i risultati in FILTEREDLEVELS.</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Raggruppa le righe in base al livello di registrazione e memorizza i risultati in GROUPEDLEVELS.</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Crea un nuovo set di dati che contiene ciascun valore univoco del livello di registrazione e il numero di occorrenze. Viene archiviato in FREQUENCIES.</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordina i livelli di registrazione per conteggio (decrescente) e archivia li archivia in RESULT.</td>
	</tr>
	</table>

6. È anche possibile salvare i risultati di una trasformazione usando l'istruzione `STORE`. Ad esempio, la seguente istruzione salva il valore `RESULT` nella directory **/example/data/pigout** nel contenitore di archiviazione predefinito per il cluster.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] I dati vengono memorizzati nella directory specificata nei file denominati **part-nnnnn**. Se la directory esiste già, si riceverà un errore.

7. Per uscire dal prompt grunt, immettere la seguente istruzione.

		QUIT;

###File batch di Pig Latin

È inoltre possibile usare il comando Pig per eseguire processi Pig Latin contenuti in un file.

3. Dopo aver chiuso il prompt grunt, usare il seguente comando per indirizzare STDIN in un file denominato **pigbatch.pig**. Questo file verrà creato nella directory principale dell'account al quale è stata eseguita la registrazione per la sessione SSH.

		cat > ~/pigbatch.pig

4. Digitare o incollare le righe seguenti e quindi premere CTRL+D al termine.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Usare quanto segue per eseguire il file **pigbatch.pig** mediante il comando Pig.

		pig ~/pigbatch.pig

	Al termine del processo batch, si otterrà il seguente output, corrispondente a quello ottenuto quando è stato usato `DUMP RESULT;` nei passaggi precedenti.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>Riepilogo

Come si può notare, il comando Pig consente di eseguire operazioni MapReduce in modo interattivo con Pig Latin, nonché di eseguire istruzioni archiviate in un file batch.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight.

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=47-->
