<properties 
	pageTitle="Spostare i dati in SQL Server in un macchina virtuale di Azure | Azure" 
	description="Spostare i dati da file flat o da un Server SQL locale a SQL Server su VM di Azure" 
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
	ms.date="02/10/2016" 
	ms.author="fashah;mohabib;bradsev" />

# Spostamento dei dati in SQL Server in una macchina virtuale di Azure

## Introduzione

**In questo documento** vengono descritte le opzioni per lo spostamento dei dati da file flat (formati CSV o TSV) o da un Server SQL locale a SQL Server o in una macchina virtuale Azure. Queste attività per lo spostamento dei dati nel cloud fanno parte del Cortana Analytics Process fornito da Azure.

Per un argomento che descrive le opzioni per lo spostamento dei dati a un Database di SQL Azure per Machine Learning, vedere [Spostare i dati a un Database di SQL Azure per Azure Machine Learning](machine-learning-data-science-move-sql-azure.md).

Il **menu** seguente collega ad argomenti che descrivono come inserire dati in ambienti di destinazione dove i dati possono essere archiviati ed elaborati durante il processo Cortana Analytics Process (CAP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


Nella tabella seguente vengono riepilogate le opzioni per lo spostamento dei dati in SQL Server in una macchina virtuale Azure.

<b>SOURCE</b> |<b>DESTINAZIONE: SQL Server in VM di Azure</b> |
------------------ |-------------------- |
<b>File Flat</b> |1\. <a href="#insert-tables-bcp">Utilità copia di massa della riga di comando (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Inserimento di massa query SQL</a><br> 3. <a href="#sql-builtin-utilities">Utilità grafiche integrate in SQL Server</a>
<b>Server SQL locale</b> | 1\. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure</a><br> 2. <a href="#export-flat-file">Esportazione in un file flat </a><br> 3. <a href="#sql-migration">Migrazione guidata database SQL </a> <br> 4. <a href="#sql-backup">Backup e ripristino database </a><br>

Tenere presente che il presente documento presuppone che i comandi SQL vengano eseguiti da SQL Server Management Studio o Visual Studio Database Explorer.

> [AZURE.TIP] In alternativa, è possibile usare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per creare e pianificare una pipeline che sposta i dati a una macchina virtuale di SQL Server in Azure. Per altre informazioni, vedere [Copia di dati con Data factory di Azure (Attività di copia)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Prerequisiti
Il tutorial presuppone:

* Un **sottoscrizione Azure**. Se non si dispone di una sottoscrizione, è possibile iscriversi per provare la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un **account di archiviazione Azure**. In questa esercitazione si userà un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](../storage/storage-create-storage-account.md#create-a-storage-account). Dopo avere creato l'account di archiviazione, sarà necessario ottenere la chiave dell'account usata per accedere alla risorsa di archiviazione. Vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Provisioning di **SQL Server in una VM di Azure**. Per le istruzioni, vedere [Configurare una macchina virtuale SQL Server di Azure come server IPython Notebook per l'analisi avanzata](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Installazione e configurazione di **Azure PowerShell** in locale. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a> Spostamento di dati da un'origine di file flat a SQL Server su una VM di Azure

Se i dati si trovano in un file flat (organizzati in un formato righe/colonne), possono essere spostati a una macchina virtuale di SQL Server attraverso i seguenti metodi:

1. [Utilità copia di massa della riga di comando (BCP)](#insert-tables-bcp) 
2. [Inserimento di massa query SQL ](#insert-tables-bulkquery)
3. [Utilità grafiche integrate in SQL Server (importazione/esportazione, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilità copia di massa della riga di comando (BCP)

BCP è un'utilità della riga di comando installata con SQL Server e rappresenta uno dei metodi più rapidi per spostare i dati. Funziona in tutte e tre le varianti di SQL Server (SQL Server locale, SQL Azure e macchine virtuali SQL Server in Azure).

> [AZURE.NOTE] **Dove devono trovarsi i dati per eseguire la copia BCP?** Anche se non è obbligatorio, se i file contenenti di dati di origine si trovano nella stessa macchina del server SQL di destinazione, i trasferimenti saranno più rapidi (velocità di rete rispetto a velocità IO del disco locale). È possibile spostare i file flat contenenti i dati nel computer dove è installato SQL Server utilizzando diversi strumenti per la copia dei file quali [AZCopy](../storage/storage-use-azcopy.md), [Esplora archivi Azure](http://storageexplorer.com/) o la funzione di copia/incolla di Windows tramite Remote Desktop Protocol (RDP).

1. Assicurarsi che il database e le tabelle vengano create nel database di SQL Server di destinazione. Ecco un esempio di come procedere utilizzando i comandi `Create Database` e `Create Table`:

		CREATE DATABASE <database_name>
	
		CREATE TABLE <tablename>
		(
			<columnname1> <datatype> <constraint>,
			<columnname2> <datatype> <constraint>,
			<columnname3> <datatype> <constraint>
		) 
	
2. Generare il file di formato che descrive lo schema per la tabella eseguendo il comando seguente dalla riga di comando del computer in cui è installato bcp.

	`bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

3. Inserire i dati nel database utilizzando il comando bcp come indicato di seguito. Dovrebbe funzionare dalla riga di comando, presupponendo che SQL Server sia installato nello stesso computer:

	`bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Ottimizzazione inserimenti BCP** Per ottimizzare gli inserimenti, fare riferimento al seguente articolo ["Linee guida per ottimizzare l'importazione di massa"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx).

### <a name="insert-tables-bulkquery-parallel"></a>Parallelizzazione delle operazioni di inserimento per uno spostamento dei dati più veloce

Se i dati che si stanno spostando sono grandi, è possibile velocizzare l'operazione eseguendo contemporaneamente più comandi BCP in uno script di PowerShell.

> [AZURE.NOTE] **Inserimento di Big Data** Per ottimizzare il caricamento dei dati per set di dati grandi e molto grandi, partizionare le tabelle dei database logici e fisici mediante più filegroup e tabelle di partizione. Per ulteriori informazioni sulla creazione e sul caricamento dei dati in tabelle di partizione, vedere [Caricamento parallelo di tabelle di partizione SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


Lo script PowerShell di esempio illustra gli inserimenti mediante bcp:
	
	$NO_OF_PARALLEL_JOBS=2

	 Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
	 # Define what each job does
	   $ScriptBlock = {
		   param($partitionnumber)

		   #Explictly using SQL username password
		   bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

			#Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
			#bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
	  }
	

	# Background processing of all partitions
	for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
	{
	  Write-Debug "Submit loading partition # $i"
	  Start-Job $ScriptBlock -Arg $i	  
	}
	

	# Wait for it all to complete
	While (Get-Job -State "Running")
	{
	  Start-Sleep 10
	  Get-Job
	}
	
	# Getting the information back from the jobs
	Get-Job | Receive-Job
	Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Inserimento di massa query SQL

L'[inserimento di massa di query SQL](https://msdn.microsoft.com/library/ms188365) può essere utilizzato per importare dati nel database da file basati su righe/colonne (i tipi supportati sono indicati nell’argomento [Preparazione dei dati per l’esportazione o l’importazione di massa (SQL Server) ](https://msdn.microsoft.com/library/ms188609)).

Ecco alcuni comandi di esempio per l'inserimento di massa:

1. Analizzare i dati e impostare le opzioni personalizzate prima dell'importazione per assicurarsi che il database SQL Server presupponga lo stesso formato per tutti i campi speciali, ad esempio le date. Ecco un esempio di come impostare il formato della data come anno-mese-giorno (se i dati contengono la data in formato anno-mese-giorno):

		SET DATEFORMAT ymd;	
	
2. portare i dati utilizzando le istruzioni per eseguire importazioni di massa

    	BULK INSERT <tablename>
    	FROM	
    	'<datafilename>'
    	WITH 
    	(
		FirstRow=2,
    	FIELDTERMINATOR =',', --this should be column separator in your data
    	ROWTERMINATOR ='\n'   --this should be the row separator in your data
    	)
 	  

### <a name="sql-builtin-utilities"></a>Utilità integrate in SQL Server

È possibile utilizzare SQL Server Integrations Services (SSIS) per importare i dati nelle macchine virtuali SQL Server in Azure da un file flat. SSIS è disponibile in due ambienti studio. Per ulteriori informazioni, vedere [Integration Services (SSIS) e ambienti Studio](https://technet.microsoft.com/library/ms140028.aspx):

- Per informazioni dettagliate su SQL Server Data Tools, vedere [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- Per informazioni dettagliate sull'importazione/esportazione guidata, vedere [Importazione/esportazione guidata di SQL Server](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Spostamento dei dati da SQL Server locale a SQL Server in una VM di Azure

È inoltre possibile utilizzare le strategie di migrazione seguenti:

1. [Distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Esportazione in un file flat](#export-flat-file) 
3. [Migrazione guidata database SQL](#sql-migration)
4. [Backup e ripristino database](#sql-backup)

Tali procedure vengono descritte qui di seguito:

### Distribuzione di un database di SQL Server a una macchina virtuale di Microsoft Azure

La **Distribuzione di un Database SQL Server in una macchina virtuale di Microsoft Azure** è un modo semplice e consigliato per spostare dati da un'istanza di SQL Server locale a un SQL Server in una macchina virtuale di Azure. Per passaggi dettagliati, nonché per una descrizione delle altre alternative, vedere [Migrazione di un database a SQL Server su una macchina virtuale di Azure](../virtual-machines/virtual-machines-migrate-onpremises-database.md).

### <a name="export-flat-file"></a>Esportazione in un file flat

È possibile utilizzare diversi metodi per l'esportazione di massa dei dati dal Server locale SQL come descritto nell’argomento [Importazione ed esportazione dei dati in massa (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx). In questo documento si parla di Bulk Copy Program (BCP) come esempio. Una volta che i dati sono esportati in un file flat, possono essere importati in un altro server SQL mediante l'importazione di massa.

1. Esportare i dati dal Server locale SQL in un file mediante l'utilità bcp come indicato di seguito

	`bcp dbname..tablename out datafile.tsv -S	servername\sqlinstancename -T -t \t -t \n -c`

2. Creare il database e la tabella nella macchina virtuale di SQL Server in Azure tramite `create database` e `create table` per lo schema della tabella esportato nel passaggio 1.

3. Creare un file di formato per la descrizione dello schema della tabella dei dati da importare/esportare. I dettagli del file di formato sono descritti in [Creazione di un file di formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

	Creazione di file di formato quando si esegue BCP dal computer SQL Server

		bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

	Creazione di file di formato quando si esegue BCP in remoto rispetto a SQL Server

		bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
		
	
4. Utilizzare uno dei metodi descritti nella sezione [Spostamento dei dati dall'origine file](#filesource_to_sqlonazurevm) per spostare i dati dai file flat in SQL Server.

### <a name="sql-migration"></a>Migrazione guidata database SQL

[Migrazione guidata database SQL Server](http://sqlazuremw.codeplex.com/) fornisce un modo semplice per spostare i dati tra due istanze del server SQL. Consente all'utente di mappare lo schema dei dati tra origini e tabelle di destinazione, scegliere i tipi di colonna e varie altre funzionalità. Utilizza la copia di massa (BCP) dietro le quinte. Di seguito è riportata una schermata della schermata iniziale della procedura guidata di migrazione del database SQL.

![Migrazione guidata in SQL Server][2]

### <a name="sql-backup"></a>Backup e ripristino database

SQL Server supporta:

1. La [funzionalità di backup e ripristino del database](https://msdn.microsoft.com/library/ms187048.aspx) (sia in un file locale o in un'esportazione bacpac in un BLOB) e [applicazioni livello dati](https://msdn.microsoft.com/library/ee210546.aspx) (tramite bacpac). 
2. Possibilità di creare direttamente le macchine virtuali SQL Server in Azure con un database copiato o di copiare in un database esistente di SQL Azure. Per ulteriori informazioni, vedere [Utilizzo della procedura guidata di copia del database](https://msdn.microsoft.com/library/ms188664.aspx). 

Seguito è riportata una schermata delle opzioni di backup e ripristino del database da SQL Server Management Studio.

![Strumento di importazione di SQL Server][1]

## Risorse

[Migrazione di un database a SQL Server su una macchina virtuale di Azure](../virtual-machines/virtual-machines-migrate-onpremises-database.md)

[Panoramica di SQL Server in Macchine virtuali di Azure](../virtual-machines/virtual-machines-sql-server-infrastructure-services.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

<!---HONumber=AcomDC_0309_2016-->