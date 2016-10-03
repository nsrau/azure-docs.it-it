<properties
	pageTitle="Dati di esempio nelle tabelle Hive di Azure HDInsight| Microsoft Azure"
	description="Esecuzione del sotto-campionamento dei dati nelle tabelle Hive (Hadopop) di Azure HDInsight"
	services="machine-learning,hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard" 
	editor="cgronlun"  />  

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="hangzh;bradsev" />

# Dati di esempio nelle tabelle Hive di Azure HDInsight

In questo articolo, viene descritto come eseguire il downsampling dei dati archiviati nelle tabelle Hive di Azure HDInsight usando le query Hive. Vengono trattati i tre metodi di campionamento utilizzati più frequentemente:

* Campionamento casuale uniforme
* Campionamento casuale per gruppi
* Campionamento stratificato

**Perché campionare i dati?** Se il set di dati da analizzare è grande, si consiglia di eseguire il downsampling dei dati per ridurli a una dimensione inferiore e più facilmente gestibile, ma comunque rappresentativa. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Processo di analisi scientifica dei dati per i team consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli di Machine Learning.

Il **menu** seguente collega ad argomenti che descrivono come campionare dati da vari ambienti di archiviazione.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Questo campionamento è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Come inviare query Hive
Le query Hive possono essere inviate dalla console della riga di comando di Hadoop nel nodo head del cluster Hadoop. Per effettuare questa operazione, accedere al nodo head del cluster Hadoop, aprire la console della riga di comando e inviare le query Hive da tale posizione. Per istruzioni su come inviare le query Hive nella console della riga di comando di Hadoop, vedere [Come inviare le query Hive](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="uniform"></a> Campionamento casuale uniforme
Nel campionamento casuale uniforme tutte le righe del set di dati hanno la stessa possibilità di essere sottoposte a campionamento. Tale metodo può essere implementato aggiungendo un ulteriore campo casuale () al set di dati relativo alla query "select" interna e a quella "select" esterna che condizionano il campo casuale.

Di seguito è fornito un esempio di query:

	SET sampleRate=<sample rate, 0-1>;
	select
		field1, field2, …, fieldN
	from
		(
		select
			field1, field2, …, fieldN, rand() as samplekey
		from <hive table name>
		)a
	where samplekey<='${hiveconf:sampleRate}'

In questo caso, `<sample rate, 0-1>` indica la proporzione di record che gli utenti desiderano campionare.

## <a name="group"></a> Campionamento casuale per gruppi

Quando si esegue il campionamento dei dati di categoria, è possibile scegliere di includere o di escludere tutte le istanze di un determinato valore di una variabile di categoria. Questo è il significato di "campionamento per gruppi". Ad esempio, se si dispone di variabile di categoria "State" con valori quali NY, MA, CA, NJ, PA e così via, è possibile che l'utente desideri che i record dello stesso stato siano sempre visualizzati insieme, che siano campionati o meno.

Di seguito è presentata una query di esempio che consente di eseguire il campionamento per gruppi:

	SET sampleRate=<sample rate, 0-1>;
    select
		b.field1, b.field2, …, b.catfield, …, b.fieldN
	from
		(
		select
			field1, field2, …, catfield, …, fieldN
		from <table name>
		)b
	join
		(
		select
			catfield
		from
			(
			select
				catfield, rand() as samplekey
			from <table name>
			group by catfield
			)a
		where samplekey<='${hiveconf:sampleRate}'
		)c
	on b.catfield=c.catfield

## <a name="stratified"></a> Campionamento stratificato

Il campionamento casuale è stratificato rispetto a una variabile di categoria, nel caso in cui i campioni ottenuti presentino, per quella categoria, valori di proporzione equivalente a quelli del popolamento padre dal quale sono stati ottenuti i campioni. Usando lo stesso esempio precedente, si supponga che i dati dispongano di un popolamento secondario in base allo stato. Ad esempio, NJ dispone di 100 osservazioni, NY dispone di 60 osservazioni e WA di 300 osservazioni. Se si specifica che la proporzione del campionamento stratificato sia pari a 0,5, il campione ottenuto deve disporre all'incirca di 50, 30 e 150 osservazioni per NJ, NY e WA

Di seguito è fornito un esempio di query:

	SET sampleRate=<sample rate, 0-1>;
    select
		field1, field2, field3, ..., fieldN, state
	from
		(
		select
			field1, field2, field3, ..., fieldN, state,
			count(*) over (partition by state) as state_cnt,
      		rank() over (partition by state order by rand()) as state_rank
      	from <table name>
		) a
	where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Per informazioni su metodi di campionamento più avanzati disponibili in Hive, vedere [Campionamento di LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
 

<!---HONumber=AcomDC_0921_2016-->