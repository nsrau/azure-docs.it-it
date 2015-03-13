<properties
   pageTitle="Usare Pig con Hadoop in HDInsight | Azure"
   description="Informazioni su come usare Pig con Hadoop in HDInsight mediante Desktop remoto."
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

# Esecuzione di processi Pig usando il comando Pig (Desktop remoto)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce una procedura dettagliata dell'uso del comando Pig per l'esecuzione di istruzioni Pig Latin in modo interattivo, o come processo batch, in un cluster Hadoop basato su Linux in HDInsight. Pig Latin consente di creare applicazioni MapReduce descrivendo le trasformazioni di dati, anziché eseguendo il mapping e la riduzione delle funzioni.

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Windows

* Un client Windows 7, 8 o 10

## <a id="connect"></a>Connettersi a Desktop remoto

Abilitare Desktop remoto per il cluster HDInsight, quindi connettersi a esso seguendo le istruzioni riportate nella pagina relativa alla <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">connessione ai cluster HDInsight tramite RDP</a>.

## <a id="pig"></a>Usare il comando Pig

2. Dopo aver effettuato la connessione, avviare la **riga di comando di Hadoop** usando l'icona sul desktop.

2. Usare il seguente comando per avviare la riga di comando di Pig.

		%pig_home%\bin\pig

	Viene visualizzato un prompt dei comandi  `grunt>`. 

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

3. Dopo aver chiuso il prompt grunt, aprire il **Blocco note** e creare un nuovo file denominato **pigbatch.pig** nella directory **%PIG_HOME %**.

4. Digitare o incollare le seguenti righe nel file **pigbatch.pig**, quindi salvare.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Usare quanto segue per eseguire il file **pigbatch.pig** usando il comando pig.

		pig %PIG_HOME%\pigbatch.pig

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
