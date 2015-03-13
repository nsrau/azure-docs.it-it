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
 
# Esecuzione di processi Pig usando il comando Pig (SSH)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Questo documento illustra il processo di connessione a un cluster HDInsight basato su Linux tramite SSH e l'uso del comando Pig per eseguire istruzioni Pig Latin in modo interattivo o come processo batch.

Il linguaggio di programmazione Pig Latin consente di descrivere le trasformazioni applicate ai dati di input per produrre l'output desiderato.

> [AZURE.NOTE] Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere l'articolo relativo alle <a href="../hdinsight-hadoop-linux-information/" target="_blank">informazioni utili su Hadoop in HDInsight basato su Linux</a>.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Linux.

* Un client SSH. I sistemi operativi Linux, Unix e Mac dovrebbero essere forniti con un client SSH. Per gli utenti di Windows è necessario scaricare un client, ad esempio <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a>

## <a id="ssh"></a>Connettersi tramite SSH

Connettersi al nome di dominio completo (FQDN) del cluster HDInsight usando il comando SSH. L'FQDN è costituito dal nome assegnato al cluster, seguito da **.azurehdinsight.net**. Ad esempio, il seguente comando stabilirà la connessione a un cluster denominato **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, sarà necessario fornire la password quando richiesto.

### PuTTY (client di Windows)

Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY **, disponibile per il download da <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Per altre informazioni sull'uso di PuTTY, vedere la sezione sull'**uso di PuTTY per la connessione a un computer Linux** nella pagina relativa a <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">come usare SSH con Linux in Azure</a>.

> [AZURE.NOTE] Se è stato usato un certificato per l'autenticazione SSH per il cluster HDInsight, sarà necessario anche leggere la sezione sulla **creazione di una chiave privata PPK per PuTTY** nella pagina relativa a <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">come usare SSH con Linux in Azure.</a>

## <a id="pig"></a>Usare il comando Pig

2. Una volta connessi, avviare l'interfaccia della riga di comando di Pig usando il seguente comando.

        pig

	Dopo qualche secondo, verrà visualizzato un prompt dei comandi `grunt>`.

3. Immettere la seguente istruzione.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Questo comando carica i contenuti del file sample. log in LOGS. È possibile visualizzare i contenuti del file usando il seguente comando.

		DUMP LOGS;

4. Successivamente, trasformare i dati applicando un'espressione regolare per estrarre solo il livello di registrazione da ciascun record usando le seguenti operazioni.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	È possibile usare **DUMP** per visualizzare i dati dopo la trasformazione. In questo caso, `DUMP LEVELS;`.

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
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Crea un nuovo set di dati che contiene ciascun valore univoco del livello di registrazione e il numero di occorrenze. I risultati vengono memorizzati in FREQUENCIES</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordina i livelli di registrazione in base al numero (decrescente) e memorizza i risultati in RESULT</td>
	</tr>
	</table>

6. È inoltre possibile salvare i risultati di una trasformazione usando l'istruzione `STORE`. Ad esempio, la seguente istruzione salva `RESULT` nella directory **/example/data/pigout** nel contenitore di archiviazione predefinito per il cluster.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] I dati vengono memorizzati nella directory specificata nei file denominati **part-nnnnn**. Se la directory esiste già, si riceverà un errore.

7. Per uscire dal prompt grunt, immettere la seguente istruzione.

		QUIT;

### File batch di Pig Latin

È inoltre possibile usare il comando Pig per eseguire processi Pig Latin contenuti in un file.

3. Dopo aver chiuso il prompt grunt, usare il seguente comando per indirizzare STDIN in un file denominato **pigbatch.pig**. Questo file verrà creato nella directory principale dell'account al quale è stata eseguita la registrazione per la sessione SSH.

		cat > ~/pigbatch.pig

4. Digitare o incollare le seguenti righe, quindi usare CTRL+D al termine.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Usare quanto segue per eseguire il file **pigbatch.pig** usando il comando pig.

		pig ~/pigbatch.pig

	Al termine del processo batch, si otterrà il seguente output, corrispondente a quello ottenuto quando è stato usato `DUMP RESULT;` nei passaggi precedenti.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

## <a id="summary"></a>Riepilogo

Come si può notare, il comando Pig consente di eseguire in modo interattivo operazioni di MapReduce con Pig Latin, nonché eseguire istruzioni archiviate in un file batch.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight.

* [Usare Pig con Hadoop in HDInsight](../hdinsight-use-pig/)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](../hdinsight-use-hive/)

* [Usare MapReduce con Hadoop in HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
