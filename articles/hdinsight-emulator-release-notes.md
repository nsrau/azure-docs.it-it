<properties linkid="hdinsight-emulator-release-notes" urlDisplayName="HDInsight Emulator release notes" pageTitle="Release notes: Microsoft HDInsight Emulator for Azure | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Get late-breaking information about the most recent releases of the HDInsight Hadoop Emulator." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Release notes: Microsoft HDInsight Emulator for Azure" authors="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cgronlun" />

# Note sulla versione: Microsoft HDInsight Emulator per Azure

> [WACOM.NOTE]
> Il modo più semplice per verificare il numero di versione consiste nell'osservare la voce "Microsoft HDInsight Emulator per Azure" (per la versione 1.0.0.0 o versioni successive) o "Microsoft HDInsight Developer Preview" (per le versioni precedenti a 1.0.0.0) in Installazione applicazioni.

## v1.0.0.0, rilasciata il 28/10/2013

-   Questa è una versione GA (General Availability) di Microsoft HDInsight Emulator per Azure, in precedenza Microsoft HDInsight Developer Preview.

-   Come le versioni preliminari del prodotto, anche questa versione è destinata a scenari di sviluppo e pertanto supporta solo distribuzioni a nodo singolo.

### Novità

-   Sono stati aggiunti script per semplificare l'impostazione di tutti i servizi Apache Hadoop per l'avvio automatico o manuale. L'impostazione predefinita prevede ancora l'avvio automatico, ma ora è possibile modificare tutti i servizi utilizzando gli script set-onebox-manualstart.cmd o set-onebox-autostart.cmd installati in C:\\Hadoop.

-   Il numero di dipendenze di installazione necessarie è stato ridotto notevolmente, per accelerare le installazioni.

-   Correzione di bug nel comando utilizzato per eseguire esempi Pig nello script RunSamples.ps1 installato nella cartella GettingStarted.

-   Questa versione contiene un aggiornamento alla versione 1.1 di Hortonworks Data Platform corrispondente ai servizi Hortonworks Data Platform disponibili nella versione 1.6 del cluster Azure HDInsight.

## v0.11.0.0, rilasciata il 30/09/2013

### Novità

-   Il dashboard HDInsight è stato rimosso.

-   Questa versione contiene un aggiornamento a Hortonworks Data Platform Developer Preview corrispondente a Hortonworks Data Platform Preview in Azure HDInsight.

## v0.10.0.0, rilasciata il 09/08/2013

### Novità

-   Questa versione contiene un aggiornamento a Hortonworks Data Platform Developer Preview corrispondente a Hortonworks Data Platform Preview in Azure HDInsight.

## v0.8.0.0, rilasciata il 07/06/2013

### Novità

-   Questa versione contiene un aggiornamento a Hortonworks Data Platform Developer Preview corrispondente a Hortonworks Data Platform Preview in Azure HDInsight.

## v0.6.0.0, rilasciata il 13/05/2013

### Novità

-   Viene installato Hive Server 2. Questo è necessario per la nuova versione 0.9.2.0 di Microsoft ODBC Driver for Hive rilasciata contemporaneamente a questo aggiornamento.

-   Tutti i servizi sono impostati per l'avvio automatico, quindi non è più necessario avviare nulla dopo un riavvio del computer.

## v0.4.0.0, rilasciata il 25/03/2013

### Novità

-   Nuova release di Microsoft HDInsight Developer Preview e di Hortonworks Data Platform for Windows Developer Preview.

-   Include Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog e Templeton.

-   Nuovo dashboard di Microsoft HDInsight con le funzionalità seguenti:

-   Connessione a più cluster, compresa l'installazione locale e quelli in esecuzione in remoto mediante il servizio Azure HDInsight.

-   Per altre informazioni sul servizio HDInsight, vedere [http://azure.microsoft.com/it-it/documentation/services/hdinsight/](http://azure.microsoft.com/it-it/documentation/services/hdinsight/).

-   Configurare WASB in un cluster locale

-   Vedere le istruzioni dettagliate riportate di seguito.

-   Creazione e modifica di query Hive nella nuova console Hive interattiva.

-   Visualizzazione e download della cronologia processi e dei risultati.

### Note sulla versione

Numeri di porta

-   Gli endpoint API REST in un'installazione locale di HDInsight e il servizio Azure HDInsight sono accessibili tramite diversi numeri di porta per gli stessi servizi:

    Locale:
    Oozie: http://localhost:11000/oozie/v1/admin/status
    Templeton: http://localhost:50111/templeton/v1/status
    ODBC: utilizzare la porta 10000 nella stringa di connessione o nella configurazione DSN.

    Servizio HDInsight:
    Oozie: http://ServerFQDN:563/oozie/v1/admin/status
    Templeton: http://ServerFQDN:563/templeton/v1/status
    ODBC: utilizzare la porta 563 nella stringa di connessione o nella configurazione DSN

-   Configurazione di ASV nel cluster locale:

    Nel dashboard sarà presente un cluster locale predefinito denominato "local (hdfs)". Se si desidera ASV come risorsa di archiviazione per l'installazione locale, eseguire le operazioni seguenti:

    1.  Aggiungere il tag account in core-site.xml disponibile in C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf:

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

    2.  Aprire la shell dei comandi di Hadoop sul desktop con privilegi elevati ed eseguire il comando seguente:

            %HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

    3.  Accedere a qualsiasi file su tale account utilizzando l'URI completo: asv://{container}@{account}/{path} (oppure asvs:// se si vuole usare HTTPS per l'accesso ai dati). Esempio:

            hadoop fs -lsr 
            asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

    4.  Eliminare il cluster locale attualmente registrato e registrarlo nuovamente con le nuove credenziali ASV.

## v0.3.0.0, rilasciata il 13/12/2012

-   Il sito Web del dashboard è stato impostato sull'autenticazione anonima anziché sull'utilizzo delle credenziali Windows. In questo modo si elimina il problema della richiesta di accesso citato nelle note sulla versione relative alla versione precedente.

-   Sono stati corretti alcuni bug di Sqoop relativi all'esportazione e ad alcuni tipi di importazione.

### Problemi della versione

-   La console JavaScript non viene caricata. Per informazioni dettagliate, vedere le note sulla versione relative alla versione 0.2.0.0.
-   Nella riga di comando di Sqoop verranno visualizzati gli avvisi riportati di seguito. Verranno corretti in un aggiornamento futuro e possono essere ignorati.

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

## v0.2.0.0, rilasciata il 03/12/2012

-   Introduzione del controllo delle versioni semantico in MSI

-   Correzione di diversi problemi di installazione segnalati sui forum MSDN, con particolare riferimento all'installazione del dashboard di HDInsight

-   Aggiunta di voci del menu di avvio per migliorare l'individuazione

-   Correzione per l'input multiriga nella console Hive

-   Aggiornamenti di minore entità al contenuto introduttivo

### Problemi della versione

-   La console JavaScript non viene caricata

    -   In alcune installazioni la console JavaScript non viene caricata e nella pagina viene visualizzato un errore HTTP 404. Per risolvere il problema, passare direttamente a http://localhost:8080 per usare la console.
-   Quando si passa al dashboard di HDInsight viene chiesto di specificare le credenziali di accesso

    -   Alcuni utenti hanno segnalato che viene visualizzata una finestra di dialogo di accesso quando si passa al dashboard di HDInsight. In questo caso è possibile specificare le credenziali di accesso dell'utente corrente per passare al dashboard.

## v1.0.0.0, rilasciata il 23/10/2012

-   Versione iniziale

### Problemi della versione

-   Console Hive

    -   Se il comando Hive inviato include un carattere di nuova riga, verrà visualizzato un errore di sintassi. Rimuovere i caratteri di nuova riga e la query dovrebbe essere eseguita nel modo previsto.

## Problemi noti generali

-   Scadenza della password utente di Hadoop

    La password dell'utente di Hadoop può scadere, a seconda dei criteri AD di cui viene eseguito il push nel computer. Il seguente script di PowerShell consente di evitare la scadenza della password e può essere eseguito al prompt dei comandi amministrativo.

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

-   Directory Temp\>

    hadoop.tmp.dir punta al percorso sbagliato, ovvero C:\\hdfs anziché C:\\hadoop\\hdfs. Questo bug verrà corretto nel prossimo aggiornamento delle parti di codice HDP.

-   Restrizioni relative al sistema operativo

    HDInsight Server deve essere installato su un sistema operativo a 64 bit

-   HDInsight non viene rilevato nei risultati della ricerca WebPI.

    Questo problema è generalmente dovuto a una limitazione relativa al sistema operativo. HDInsight richiede un sistema operativo a 64 bit che sia almeno Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack 1, Windows 8 o Windows Server 2012 o versioni successive.

-   Documentazione del prompt dei comandi amministrativo

    -   I comandi quali **hadoop mradmin** o **hadoop defadmin** possono essere eseguiti solo dall'utente hadoop.

    -   Per creare facilmente una shell eseguita con i privilegi di tale utente, eseguire quando segue al prompt dei comandi di Hadoop:

            start-hadoopadminshell

    -   Verrà aperta una nuova shell dei comandi eseguita con i privilegi di amministratore Hadoop

## <a name="nextsteps"></a>Passaggi successivi

-   [Introduzione all'utilizzo di HDInsight Emulator][Introduzione all'utilizzo di HDInsight Emulator]

  [Introduzione all'utilizzo di HDInsight Emulator]: ../hdinsight-get-started-emulator/
