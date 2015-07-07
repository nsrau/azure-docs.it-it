<properties 
	pageTitle="Note sulla versione: Microsoft HDInsight Emulator per Azure | Microsoft Azure" 
	description="Informazioni aggiornate sulle versioni più recenti di HDInsight Hadoop Emulator, un ambiente sandbox di Hadoop." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>




# Note sulla versione: Microsoft HDInsight Emulator per Hadoop 



> [AZURE.NOTE]Il modo più semplice per verificare il numero di versione consiste nell'osservare la voce **Microsoft HDInsight Emulator per Azure** (per la versione 1.0.0.0 o successive) o **Microsoft HDInsight Developer Preview** (per le versioni precedenti a 1.0.0.0) in Installazione applicazioni.

## Versione 2.0.0.0, resa disponibile il 29 agosto 2014

* Questa versione consente di aggiornare HDInsight Emulator in modo da poterlo usare con gli stessi progetti Hadoop attualmente attivi nel servizio nella versione 3.1.    

* Come per le versioni di anteprima del prodotto, anche questa versione è destinata a scenari di sviluppo e di conseguenza supporta solo le distribuzioni a nodo singolo.

### Novità 
 
* [Versioni aggiornate dei componenti di Hadoop](hdinsight-component-versioning.md) corrispondenti alla versione 3.1 del servizio. È incluso il supporto per Hive 0.13 e Tez.

## Versione 1.0.0.0, resa disponibile il 28 ottobre 2013

* Questa è una versione GA (General Availability) di Microsoft HDInsight Emulator per Azure, in precedenza Microsoft HDInsight Developer Preview. 

* Come per le versioni di anteprima del prodotto, anche questa versione è destinata a scenari di sviluppo e di conseguenza supporta solo le distribuzioni a nodo singolo.

### Novità 
 
* Sono stati aggiunti script per semplificare l'impostazione di tutti i servizi Apache Hadoop per l'avvio automatico o manuale. L'impostazione predefinita prevede ancora l'avvio automatico, ma è ora possibile modificare tutti i servizi mediante lo script set-onebox-manualstart.cmd o set-onebox-autostart.cmd, entrambi installati in C:\Hadoop. 

* Il numero di dipendenze di installazione necessarie è stato ridotto notevolmente, per accelerare le installazioni.

* Questa versione include una correzione di bug nel comando usato per eseguire esempi Pig nello script RunSamples.ps1 installato nella cartella GettingStarted.

* Questa versione contiene un aggiornamento alla versione 1.1 di Hortonworks Data Platform (HDP), che corrisponde ai servizi Hortonworks Data Platform disponibili nella versione 1.6 del cluster Azure HDInsight.

## Versione 0.11.0.0, resa disponibile il 30 settembre 2013

### Novità 
		
* Il dashboard HDInsight è stato rimosso. 

* Questa versione contiene un aggiornamento a Hortonworks Data Platform Developer Preview corrispondente a Hortonworks Data Platform Preview in Azure HDInsight.

## Versione 0.10.0.0, resa disponibile il 9 agosto 2013

### Novità 
	
* Questa versione contiene un aggiornamento a Hortonworks Data Platform Developer Preview corrispondente a Hortonworks Data Platform Preview in Azure HDInsight.

## Versione 0.8.0.0, resa disponibile il 7 giugno 2013

### Novità 

* Questa versione contiene un aggiornamento a Hortonworks Data Platform Developer Preview corrispondente a Hortonworks Data Platform Preview in Azure HDInsight.

## Versione 0.6.0.0, resa disponibile il 13 maggio 2013

### Novità 

* Viene installato Hive Server 2. Questo componente è necessario per la versione 0.9.2.0 di Microsoft ODBC Driver per Hive, resa disponibile contemporaneamente a questo aggiornamento. 

* Poiché tutti i servizi sono impostati per l'avvio automatico, non è più necessario alcun avvio dopo un riavvio del computer.

## Versione 0.4.0.0, resa disponibile il 25 marzo 2013

### Novità 

* Nuova release di Microsoft HDInsight Developer Preview e di Hortonworks Data Platform for Windows Developer Preview. 

* Include Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog e Templeton.

* Nuovo dashboard di HDInsight con le funzionalità seguenti:
 
	* Connessione a più cluster, compresa l'installazione locale e quelli in esecuzione in remoto mediante il servizio Azure HDInsight. Per altre informazioni sul servizio HDInsight, vedere [http://azure.microsoft.com/it-it/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/).
 
	* Configurazione dell'archivio BLOB di Azure nel cluster locale. Vedere le istruzioni dettagliate riportate di seguito.

	* Creazione e modifica di query Hive nella nuova console Hive interattiva.

	* Visualizzazione e download della cronologia processi e dei risultati.

### Note sulla versione 

* È possibile accedere agli endpoint dell'API REST in un'installazione locale di HDInsight e al servizio Azure HDInsight attraverso numeri di porta diversi per gli stessi servizi: 

	Locale: Oozie - http://localhost:11000/oozie/v1/admin/status Templeton - http://localhost:50111/templeton/v1/status ODBC - Usare la porta 10000 nella configurazione DSN o nella stringa di connessione

	Servizio HDInsight: Oozie - http://ServerFQDN:563/oozie/v1/admin/status Templeton - http://ServerFQDN:563/templeton/v1/status ODBC - Usare la porta 563 nella configurazione DSN o nella stringa di connessione


* Configurazione dell'archivio BLOB di Azure nel cluster locale:

	Nel dashboard sarà presente un cluster locale predefinito denominato "local (hdfs)". Se si vuole impostare l'archivio BLOB di Azure per l'archiviazione dell'installazione locale, eseguire le operazioni seguenti:

	1. Aggiungere il tag account in core-site.xml disponibile in C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		Esempio:

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Aprire la shell dei comandi di Hadoop sul desktop con privilegi elevati ed eseguire il comando seguente:
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Accedere a qualsiasi file nell'account usando l'URI completo: asv://{container}@{account}/{path} (o asvs:// se si vuole usare HTTPS per l'accesso ai dati). Esempio:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Eliminare il cluster locale attualmente registrato e registrarlo di nuovo con le nuove credenziali di archiviazione BLOB di Azure.


## Versione 0.3.0.0, resa disponibile il 13 dicembre 2012 

* Il sito Web del dashboard è stato impostato sull'autenticazione anonima invece che sull'uso delle credenziali Windows. In questo modo, è stato eliminato il problema della richiesta delle credenziali di accesso riportato nelle note sulla versione relative alla versione precedente. 

* Sono stati corretti alcuni bug di Sqoop relativi all'esportazione e ad alcuni tipi di importazione.

### Problemi della versione 

* La console JavaScript non viene caricata. Per informazioni dettagliate, vedere le note sulla versione relative alla versione 0.2.0.0. 
* Nella riga di comando di Sqoop verranno visualizzati gli avvisi riportati di seguito. Verranno corretti in un aggiornamento futuro e possono essere ignorati. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## Versione 0.2.0.0, resa disponibile il 3 dicembre 2012

* Introduzione del controllo delle versioni semantico in Windows Installer 

* Correzione di diversi bug di installazione segnalati sui forum MSDN, in particolare correlati all'installazione del dashboard di HDInsight

* Aggiunta di voci del menu **Start** per migliorare l'individuazione

* Correzione per l'input multiriga della console Hive

* Aggiornamenti di minore entità al contenuto introduttivo

### Problemi della versione 

* La console JavaScript non viene caricata. 

	* In alcune installazioni la console JavaScript non viene caricata e nella pagina viene visualizzato un errore HTTP 404. Per risolvere il problema, passare direttamente a http://localhost:8080 per usare la console. 

* Quando si passa al dashboard di HDInsight, viene richiesto di immettere le credenziali di accesso.

	* Alcuni utenti hanno segnalato la visualizzazione di una finestra di dialogo di accesso quando si passa al dashboard di HDInsight. In questo caso, è possibile specificare le informazioni di accesso dell'utente corrente per passare al dashboard. 


## Versione 1.0.0.0, resa disponibile il 23 ottobre 2012

* Versione iniziale 

### Problemi della versione 

* Console Hive 

	* Se il comando Hive inviato include un carattere di nuova riga, verrà restituito un errore di sintassi. Rimuovere i caratteri di nuova riga e la query dovrebbe essere eseguita nel modo previsto. 



## Problemi noti generali

* Scadenza della password utente di Hadoop 

	La password dell'utente di Hadoop può scadere, a seconda dei criteri di Active Directory di cui viene eseguito il push nel computer. Lo script di Windows PowerShell seguente permette di evitare la scadenza della password e può essere eseguito da un prompt dei comandi amministrativo.

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Directory Temp
	
	Il file hadoop.tmp.dir punta al percorso sbagliato, ovvero C:\hdfs anziché C:\hadoop\hdfs. Questo bug verrà corretto nel prossimo aggiornamento delle parti di codice HDP.

* Restrizioni relative al sistema operativo

	Il server HDInsight deve essere installato su un sistema operativo a 64 bit.

* Risultati delle ricerche dell'Installazione guidata piattaforma Web (WebPI)

	HDInsight non viene rilevato nei risultati della ricerca WebPI. Questo problema è generalmente dovuto a una limitazione relativa al sistema operativo. HDInsight richiede un sistema operativo a 64 bit che sia almeno Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack 1, Windows 8 o Windows Server 2012.

* Documentazione del prompt dei comandi amministrativo

	* Comandi come **hadoop mradmin** o **hadoop defadmin** possono essere eseguiti solo come utente di Hadoop. 

	* Per creare facilmente una shell eseguita come utente di Hadoop, aprire un prompt dei comandi di Hadoop ed eseguire il comando seguente:
	 
	 		start-hadoopadminshell

	* Verrà avviata una nuova shell dei comandi, eseguita con i privilegi di amministratore di Hadoop.

##<a name="nextsteps"></a> Passaggi successivi

- [Introduzione a HDInsight Emulator][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md

 

<!---HONumber=62-->