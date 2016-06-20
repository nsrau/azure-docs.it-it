<properties
	pageTitle="Scenari relativi ad ADAPT (Advanced Analytics Process and Technology) in Azure Machine Learning | Microsoft Azure"
	description="Selezionare gli scenari appropriati per il processo di analisi predittiva avanzata in Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016" 
	ms.author="bradsev" />


# Scenari per l'analisi avanzata in Azure Machine Learning

Questo articolo descrive le varie origini dati di esempio e gli scenari di destinazione che possono essere gestiti con Cortana Analytics Process. Vengono inoltre illustrate le opzioni disponibili nelle sequenze di elaborazione basate su caratteristiche dei dati, posizioni delle origini e repository di destinazione in Azure.

L’**albero delle decisioni** per la scelta degli scenari di esempio appropriati per i dati dell’utente e l'obiettivo sono presentati nella sezione precedente.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Ciascuna delle sezioni seguenti presenta uno scenario di esempio. Per ogni scenario vengono elencati un flusso di dati scientifici o di analisi avanzata possibile e le risorse di Azure di supporto.

>[AZURE.NOTE] **Per tutti gli scenari seguenti, è necessario:**

*   [Creare un account di archiviazione](../storage/storage-create-storage-account.md)
*   [Creare un'area di lavoro Azure ML](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Scenario n. 1: Set di dati tabulari medio-piccolo in un file locale

![File locali medio-piccoli][1]

#### Risorse di Azure aggiuntive: nessuna

1.  Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

2.  Caricare un set di dati.

3.  Compilare un flusso di esperimento di Azure Machine Learning iniziando con il set di dati caricato.

## <a name="smalllocalprocess"></a>Scenario n. 2: Set di dati medio-piccolo in un file locale che richiede elaborazione

![File locali medio piccoli con elaborazione][2]

#### Risorse di Azure aggiuntive: macchina virtuale di Azure (server IPython Notebook)

1.  Creare una macchina virtuale di Azure Virtual Machine che esegue IPython Notebook.

2.  Caricare i dati in un contenitore di archiviazione di Azure.

3.  Pre-elaborare e pulire i dati in IPython Notebook, accedere ai dati dal contenitore di archiviazione di Azure.

4.  Trasformare i dati in un formato tabulare pulito.

5.  Salvare i dati trasformati in BLOB di Azure.

6.  Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

7.  Leggere i dati dal BLOB di Azure utilizzando il modulo [Import Data][import-data].

8. Compilare un flusso di esperimento di Azure Machine Learning iniziando con il set di dati inserito.

## <a name="largelocal"></a>Scenario n. 3: Set di dati di grandi dimensioni in file locali, con destinazione BLOB di Azure

![File locali di grandi dimensioni][3]

#### Risorse di Azure aggiuntive: macchina virtuale di Azure (server IPython Notebook)

1.  Creare una macchina virtuale di Azure Virtual Machine che esegue IPython Notebook.

2.  Caricare i dati in un contenitore di archiviazione di Azure.

3.  Pre-elaborare e pulire i dati in IPython Notebook, accedere ai dati dai BLOB di Azure.

4.  Trasformare i dati in un formato tabulare pulito, se necessario.

5.  Esaminare i dati e creare le funzionalità necessarie.

6.  Estrarre un campione di dati medio-piccolo.

7.  Salvare i dati campionati in BLOB di Azure.

8. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

9. Leggere i dati dal BLOB di Azure utilizzando il modulo [Import Data][import-data].

10. Compilare un flusso di esperimento di Azure ML iniziando con il set di dati inserito.


## <a name="smalllocaltodb"></a>Scenario n. 4: Set di dati medio-piccolo in un file locale, con destinazione SQL Server in una macchina virtuale di Azure

![File locali medio-piccoli al database SQL in Azure][4]

#### Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)

1.  Creare una macchina virtuale di Azure Virtual Machine che esegue SQL Server e IPython Notebook.

2.  Caricare i dati in un contenitore di archiviazione di Azure.

3.  Pre-elaborare e pulire i dati nel contenitore di archiviazione di Azure usando IPython Notebook.

4.  Trasformare i dati in un formato tabulare pulito, se necessario.

5.  Salvare i dati in file nella macchina virtuale locale (IPython Notebook è in esecuzione nella VM, le unità locali fanno riferimento alle unità della VM).

6.  Caricare i dati nel database SQL Server in esecuzione in una VM di Azure.

    a. Opzione n. 1: Uso di SQL Server Management Studio

		i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b. Option n. 2: Uso di IPython Notebook, non è consigliabile per i set di dati di dimensioni medio-grandi

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  Esaminare i dati e creare le funzionalità necessarie. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.

8. Scegliere le dimensioni del campione di dati, se necessario e/o lo si desidera.

9. Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

10. Leggere i dati direttamente da SQL Server usando il modulo [Import Data][import-data]. Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data].

11. Compilare un flusso di esperimento di Azure ML iniziando con il set di dati inserito.

## <a name="largelocaltodb"></a>Scenario n. 5: Set di dati di grandi dimensioni in file locali, con destinazione SQL Server in una macchina virtuale di Azure

![File locali di grandi dimensioni al database SQL in Azure][5]

#### Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)

1.  Creare una macchina virtuale di Azure che esegue SQL Server e il server IPython Notebook.

2.  Caricare i dati in un contenitore di archiviazione di Azure.

3.  (Facoltativo) Pre-elaborare e pulire i dati.

    a. Pre-elaborare e pulire i dati in IPython Notebook, accedere ai dati dai BLOB di Azure.

    b. Trasformare i dati in un formato tabulare pulito, se necessario.

    c. Salvare i dati in file nella macchina virtuale locale (IPython Notebook è in esecuzione nella VM, le unità locali fanno riferimento alle unità della VM).

4.  Caricare i dati nel database SQL Server in esecuzione in una VM di Azure.

    a. Accedere alla macchina virtuale di SQL Server.

    b. Se i dati non sono ancora stati salvati, scaricare i file di dati dal contenitore di archiviazione di Azure nella cartella della VM locale.

    c. Eseguire SQL Server Management Studio

    d. Creare tabelle di database e di destinazione

	e. Usare uno dei metodi di importazione in blocco per caricare i dati.

    f. Se sono richiesti join di tabella, creare gli indici per velocizzarli.

 > [AZURE.NOTE] Per caricare più velocemente dati di grandi dimensioni, è consigliabile creare tabelle partizionate e importare in blocco i dati in parallelo. Per altre informazioni, vedere l'articolo relativo all'[importazione parallela di dati in tabelle partizionale di SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Esaminare i dati e creare le funzionalità necessarie. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.

6.  Scegliere le dimensioni del campione di dati, se necessario e/o lo si desidera.

7.  Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

8. Leggere i dati direttamente da SQL Server utilizzando il modulo [Import Data][import-data]. Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data].

9. Semplice flusso di esperimento di Azure ML a partire dal set di dati caricato

## <a name="largedbtodb"></a>Scenario n. 6: Set di dati di grandi dimensioni in un database SQL Server locale, con destinazione SQL Server nella macchina virtuale di Azure

![Database SQL locale di grandi dimensioni al database SQL locale in Azure][6]

#### Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)

1.  Creare una macchina virtuale di Azure che esegue SQL Server e il server IPython Notebook.

2.  Usare uno dei metodi di esportazione dei dati per esportare i dati da SQL Server in file di dump.

    a. Nota: se si decide di spostare tutti i dati dal database locale, un metodo alternativo più rapido consiste nello spostamento dell'intero database nell'istanza di SQL Server in Azure. Ignorare i passaggi per esportare i dati, creare il database e caricare/importare i dati nel database di destinazione e seguire il metodo alternativo.

3.  Caricare file di dump nel contenitore di archiviazione di Azure.

4.  Caricare i dati in un database di SQL Server in esecuzione in una macchina virtuale di Azure.

    a. Accedere alla macchina virtuale di SQL Server.

    b. Scaricare i file di dati dal contenitore di archiviazione di Azure nella cartella della VM locale.

    c. Eseguire SQL Server Management Studio

    d. Creare tabelle di database e di destinazione

	e. Usare uno dei metodi di importazione in blocco per caricare i dati.

	f. Se sono richiesti join di tabella, creare gli indici per velocizzarli.

> [AZURE.NOTE] Per caricare più velocemente dati di grandi dimensioni, creare tabelle partizionate e importare in blocco i dati in parallelo. Per altre informazioni, vedere l'articolo relativo all'[importazione parallela di dati in tabelle partizionale di SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Esaminare i dati e creare le funzionalità necessarie. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.

6.  Scegliere le dimensioni del campione di dati, se necessario e/o lo si desidera.

7.  Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

8. Leggere i dati direttamente da SQL Server utilizzando il modulo [Import Data][import-data]. Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data].

9. Semplice flusso di esperimento di Azure ML a partire dal set di dati caricato.

### Metodo alternativo per copiare un intero database da un Server SQL locale al database SQL Azure

![Scollegare il database locale e collegarlo al database SQL in Azure][7]

#### Risorse di Azure aggiuntive: macchina virtuale di Azure (SQL Server/server IPython Notebook)

Per replicare l'intero database SQL Server nella macchina virtuale di SQL Server, è necessario copiare un database da un percorso/server a un altro, supponendo che il database possa essere portato temporaneamente offline. Eseguire questa operazione nell'interfaccia utente grafica di Esplora oggetti in SQL Server Management Studio oppure i comandi Transact-SQL equivalenti.

1. Scollegare il database nel percorso di origine. Per altre informazioni, vedere [Scollegare un database](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. In Esplora risorse o nella finestra del prompt dei comandi di Windows copiare il file o i file del database scollegato nel percorso di destinazione della macchina virtuale di SQL Server in Azure.
3. Collegare i file copiati all'istanza di SQL Server di destinazione. Per altre informazioni, vedere [Collegare un database](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Spostare un database tramite la funzionalità di scollegamento e collegamento (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Scenario n. 7: Set di dati di grandi dimensioni in file locali, con destinazione il database Hive nei cluster Hadoop di Azure HDInsight

![Big Data nell'Hive di destinazione locale][9]

#### Risorse di Azure aggiuntive: cluster Hadoop di Azure HDInsight e macchina virtuale di Azure (server IPython Notebook)

1.  Creare una macchina virtuale di Azure che esegue il server IPython Notebook.

2.  Creare un cluster Hadoop di Azure HDInsight

3.  (Facoltativo) Pre-elaborare e pulire i dati.

    a. Pre-elaborare e pulire i dati in IPython Notebook, accedere ai dati dai BLOB di Azure.

    b. Trasformare i dati in un formato tabulare pulito, se necessario.

    c. Salvare i dati in file nella macchina virtuale locale (IPython Notebook è in esecuzione nella VM, le unità locali fanno riferimento alle unità della VM).

4.  Caricare i dati nel contenitore predefinito del cluster Hadoop selezionato nel passaggio 2.

5.  Caricare i dati nel database di Hive del cluster Hadoop di HDInsight di Azure.

    a. Accedere al nodo head del cluster Hadoop

    b. Aprire la riga di comando di Hadoop.

    c. Immettere la directory radice di Hive con il comando `cd %hive_home%\bin` nella riga di comando di Hadoop.

    d. Eseguire le query Hive per creare tabelle e database e caricare i dati dall'archivio BLOB nelle tabelle Hive.

 	> [AZURE.NOTE] Se i dati sono di grandi dimensioni, gli utenti possono creare la tabella Hive con partizioni. Gli utenti possono quindi usare un ciclo `for` nella riga di comando di Hadoop nel nodo head per caricare dati nella tabella Hive partizionata in base alla partizione.

6.  Esaminare i dati e creare le funzionalità necessarie nella riga di comando di Hadoop. Si noti che le funzionalità non devono essere materializzare nelle tabelle del database. Notare solo la query necessaria per crearle.

	a. Accedere al nodo head del cluster Hadoop

    b. Aprire la riga di comando di Hadoop.

    c. Immettere la directory radice di Hive con il comando `cd %hive_home%\bin` nella riga di comando di Hadoop.

	d. Eseguire le query Hive nella riga di comando di Hadoop nel nodo head del cluster Hadoop per esplorare i dati e creare le funzionalità necessarie.

7.  Se necessario e/o lo si desidera, campionare i dati in modo da adattarli a Azure Machine Learning Studio.

8.  Accedere a [Azure Machine Learning Studio](https://studio.azureml.net/).

9. Leggere i dati direttamente da `Hive Queries` utilizzando il modulo [Import Data][import-data]. Incollare la query richiesta che estrae i campi, crea le funzionalità ed esegue il campionamento dei dati, se necessario, direttamente nella query di [Import Data][import-data].

10. Semplice flusso di esperimento di Azure ML a partire dal set di dati caricato.

## <a name="decisiontree"></a>Albero delle decisioni per la scelta degli scenari
------------------------

Nel diagramma seguente sono riepilogati gli scenari descritti in precedenza e il processo di analisi avanzata e le scelte di tecnologia effettuate che conducono l’utente a ciascuno degli scenari elencati. Tenere presente che l'elaborazione dei dati, l'esplorazione, la progettazione delle funzionalità e il campionamento potrebbero avvenire in uno o più metodi/ambienti, ad esempio nell'ambiente di origine, intermedio e/o di destinazione, e potrebbero procedere in modo iterativo, se necessario. Il diagramma è solo un'illustrazione di alcuni dei possibili flussi e non fornisce un'enumerazione completa.

![Scenari della procedura dettagliata del processo di analisi scientifica][8]

### Analisi avanzata in esempi di azioni

Per procedure dettagliate end-to-end di Azure Machine Learning che usano ADAPT tramite set di dati pubblici, vedere:


* [Il Cortana Analytics Process in azione: mediante SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Il Cortana Analytics Process in azione: mediante i cluster Hadoop di HDInsight](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0608_2016-->