<properties
	pageTitle="Configurazione di protezione di dati Oracle in macchine virtuali | Microsoft Azure"
	description="Esercitazione che illustra come configurare e implementare Oracle Data Guard in macchine virtuali di Azure a fini della disponibilità elevata e del ripristino di emergenza."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Configurazione di Oracle Data Guard per Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


In questa esercitazione viene illustrato come configurare e implementare Oracle Data Guard in un ambiente con macchine virtuali di Azure a fini della disponibilità elevata e del ripristino di emergenza. L'esercitazione è incentrata sulla replica unidirezionale per i database Oracle non RAC.

Oracle Data Guard supporta la protezione dei dati e il ripristino di emergenza per Oracle Database. Si tratta di una soluzione pronta, semplice e ad alte prestazioni per il ripristino di emergenza, la protezione dei dati e la disponibilità elevata per l'intero database Oracle.

In questa esercitazione si presuppone che si disponga già di conoscenze teoriche e pratiche sui concetti relativi al ripristino di emergenza e alla disponibilità elevata per Oracle Database. Per informazioni, vedere il [sito web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) e anche la [guida ai concetti e all'amministrazione di Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17022/create_ps.htm).

Inoltre, nell'esercitazione si presuppone che siano già stati implementati i prerequisiti seguenti:

- Lettura della sezione dedicata alle considerazioni sulla disponibilità elevata e il ripristino di emergenza nell'argomento [Immagini di macchine virtuali Oracle - considerazioni varie](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md). Si noti che Azure attualmente supporta istanze autonome di Oracle Database, ma non cluster Oracle RAC (Oracle Real Application Cluster).

- Creazione di due macchine virtuali (VM) in Azure usando la stessa immagine di Oracle Enterprise Edition fornita dalla piattaforma in Windows Server. Per informazioni, vedere [Creazione di una macchina virtuale di Oracle Database 12c in Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) e [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Verifica che le macchine virtuali si trovino nello [stesso servizio cloud](virtual-machines-load-balance.md) e nella stessa [rete virtuale](azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente. È inoltre consigliabile posizionare le macchine virtuali nello stesso [set di disponibilità](virtual-machines-manage-availability.md) per consentire ad Azure di inserirle in domini di errore e domini di aggiornamento separati. Si noti che Oracle Data Guard è disponibile solo con Oracle Database Enterprise Edition. Ogni computer deve disporre di almeno 2 GB di memoria e 5 GB di spazio su disco. Per informazioni aggiornate sulle dimensioni delle macchine virtuali fornite dalla piattaforma, vedere [Dimensioni delle macchine virtuali per Azure](http://msdn.microsoft.com/library/dn197896.aspx). Se è necessario volume su disco aggiuntivo per le macchine virtuali, è possibile collegare dischi aggiuntivi. Per informazioni, vedere [Come collegare un disco dati a una macchina virtuale](storage-windows-attach-disk.md).

- Impostazione dei nomi di macchina virtuale "Machine1" per la VM primaria e "Machine2" per la VM di standby nel portale di Azure classico.

- Impostazione della variabile di ambiente **ORACLE\_HOME** in modo che punti allo stesso percorso di installazione radice Oracle nelle macchine virtuali primaria e di standby, ad esempio `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Accesso al server Windows come membro del gruppo **Administrators** o come membro del gruppo **ORA\_DBA**.

In questa esercitazione si apprenderà come:

Implementare l'ambiente del database di standby fisico

1. Creare un database primario

2. Preparare il database primario per la creazione del database di standby

	1. Abilitare la registrazione forzata

	2. Creare un file di password

	3. Configurare un log di ripristino di standby

	4. Abilita l'archiviazione

	5. Impostare i parametri di inizializzazione del database primario

Creare un database di standby fisico

1. Preparare un file di parametri di inizializzazione per il database di standby

2. Configurare il listener e tnsnames per supportare il database nelle macchine primaria e di standby

	1. Configurare listener.ora in entrambi i server per contenere le voci per entrambi i database

	2. Configurare tnsnames.ora nelle macchine virtuali primaria e di standby per contenere le voci per i database primario e di standby

	3. Avviare il listener e controllare tnsping in entrambe le macchine virtuali per entrambi i servizi.

3. Avviare l'istanza di standby in stato nomount

4. Usare RMAN per clonare il database e creare un database di standby

5. Avviare il database di standby fisico in modalità di ripristino gestito

6. Verificare il database di standby fisico

> [AZURE.IMPORTANT] Questa esercitazione è stata predisposta e testata con la seguente configurazione hardware e software:
>
>| | **Database primario** | **Database di standby** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Versione di Oracle** | Oracle11g Enterprise Release (11.2.0.4.0) | Oracle11g Enterprise Release (11.2.0.4.0) |
>| **Nome della macchina** | Machine1 | Machine2 |
>| **Sistema operativo** | Windows 2008 R2 | Windows 2008 R2 |
>| **SID Oracle** | TEST | TEST\_STBY |
>| **Memoria** | Min 2 GB | Min 2 GB |
>| **Spazio su disco** | Min 5 GB | Min 5 GB |

Per le versioni successive di Oracle Database e Oracle Data Guard potrebbero essere presenti alcune modifiche aggiuntive da implementare. Per informazioni aggiornate specifiche della versione, vedere la documentazione relativa a [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) nel sito Web di Oracle.

##Implementare l'ambiente del database di standby fisico
### 1\. Creare un database primario

- Creare un database primario "TEST" nella macchina virtuale primaria. Per informazioni, vedere Creazione e configurazione di un Oracle Database.
- Connettersi al database come utente SYS con ruolo SYSDBA nel prompt dei comandi di SQL*Plus ed eseguire l'istruzione seguente per visualizzare il nome del database:

		SQL> select name from v$database;

		The result will display like the following:

		NAME
		---------
		TEST
- Eseguire quindi una query per recuperare i nomi dei file di database dalla vista di sistema dba\_data\_files:

		SQL> select file_name from dba_data_files;
		FILE_NAME
		-------------------------------------------------------------------------------
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. Preparare il database primario per la creazione del database di standby

Prima di creare un database di standby, è consigliabile verificare che il database primario sia configurato correttamente. Di seguito è riportato un elenco di passaggi che è necessario eseguire:

1. Abilitare la registrazione forzata
2. Creare un file di password
3. Configurare un log di ripristino di standby
4. Abilita l'archiviazione
5. Impostare i parametri di inizializzazione del database primario

#### Abilitare la registrazione forzata

Per implementare un database di standby, è necessario abilitare 'Force Logging' nel database primario. Questa opzione assicura che anche nel caso in cui venga eseguita un'operazione 'nologging', la registrazione forzata abbia la precedenza e tutte le operazioni vengano registrate nei log di ripristino. Per questo motivo, è necessario assicurarsi che tutto venga registrato nel database primario e che la replica nel database di standby includa tutte le operazioni nel database primario. Eseguire l'istruzione ALTER DATABASE per abilitare la registrazione forzata:

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### Creare un file di password

Per riuscire a distribuire e applicare i log archiviati dal server primario al server di standby, la password sys deve essere identica nel server primario e in quello di standby. Questo è il motivo per cui si crea un file di password nel database primario e lo si copia nel server di standby.

>[AZURE.IMPORTANT] Se si usa Oracle Database 12c, esiste un nuovo utente **SYSDG**, che è possibile usare per amministrare Oracle Data Guard. Per altre informazioni, vedere le [modifiche della versione Oracle Database 12c](http://docs.oracle.com/cd/E16655_01/server.121/e10638/release_changes.htm).

Assicurarsi inoltre che l'ambiente ORACLE\_HOME sia già definito in Machine1. In caso contrario, è necessario definirlo come variabile di ambiente tramite la finestra di dialogo Variabili d'ambiente. Per accedere a questa finestra di dialogo, avviare l'utilità **Sistema** facendo doppio clic sull'icona Sistema nel **Pannello di controllo**, quindi fare clic sulla scheda **Avanzate** e scegliere **Variabili d'ambiente**. Fare clic sul pulsante **Nuova** in **Variabili di sistema** per impostare le variabili di ambiente. Dopo aver impostato le variabili di ambiente, chiudere il prompt dei comandi di Windows esistente e aprirne uno nuovo.

Eseguire l'istruzione seguente per passare alla directory Oracle\_Home, ad esempio C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database.

	cd %ORACLE_HOME%\database

Creare quindi un file di password tramite l'utilità di creazione dei file di password [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). Nello stesso prompt dei comandi di Windows in Machine1, eseguire il comando seguente impostando il valore della password come password di **SYS**:

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Questo comando crea un file di password denominato PWDTEST.ora nella directory ORACLE\_HOME\\database. È necessario copiare questo file nella directory %ORACLE\_HOME%\\database in Machine2 manualmente.

#### Configurare un log di ripristino di standby

È quindi necessario configurare un log di ripristino di standby, in modo che il database primario possa ricevere correttamente il ripristino quando diventa un database di standby. La creazione preliminare di questi log in questa fase consente inoltre la creazione automatica dei log di ripristino di standby nel database di standby. È importante configurare i log di ripristino di standby con le stesse dimensioni dei log di ripristino online. Le dimensioni dei file di log di ripristino di standby correnti devono corrispondere esattamente alle dimensioni dei file di log di ripristino online del database primario corrente.

Eseguire l'istruzione seguente nel prompt dei comandi di SQL*PLUS in Machine1. v$logfile è una vista di sistema che contiene informazioni sui file di log di ripristino.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file.
	SQL> select bytes from v$log;
	BYTES
	----------
	52428800
	52428800
	52428800


Si noti che 52428800 corrisponde a 50 megabyte.

Nella finestra di SQL*Plus eseguire quindi le istruzioni seguenti per aggiungere un nuovo gruppo di file di log di ripristino di standby a un database in standby e specificare un numero che identifica il gruppo usando la clausola GROUP. L'uso dei numeri di gruppo consente di semplificare l'amministrzione dei gruppo di file di log di ripristino di standby:

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

Eseguire quindi la vista di sistema seguente per elencare informazioni sui file di log di ripristino. Questa operazione consente inoltre di verificare che siano stati creati i gruppi di file di log di ripristino di standby:

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### Abilita l'archiviazione

Abilitare quindi l'archiviazione eseguendo le istruzioni seguenti per attivare la modalità ARCHIVELOG per il database primario e abilitare l'archiviazione automatica. Per abilitare la modalità ARCHIVELOG, è possibile montare il database e quindi eseguire il comando archivelog.

In primo luogo, accedere come sysdba. Al prompt dei comandi di Windows eseguire:

	sqlplus /nolog

	connect / as sysdba

Arrestare quindi il database dal prompt dei comandi di SQL*Plus:

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

Eseguire quindi il comando startup mount per montare il database. Ciò garantisce che l'istanza venga associata da Oracle al database specificato.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

Eseguire quindi:

	SQL> alter database archivelog;
	Database altered.

Eseguire quindi l'istruzione alter database con la clausola open per rendere disponibile il database per il normale utilizzo:

	SQL> alter database open;

	Database altered.

#### Impostare i parametri di inizializzazione del database primario

Per configurare Data Guard, è prima di tutto necessario creare e configurare i parametri di standby in un normale pfile (file dei parametri di inizializzazione di testo). Quando il pfile è pronto, è necessario convertirlo in un file di parametri del server (SPFILE).

È possibile controllare l'ambiente di Data Guard tramite i parametri nel file INIT.ORA. Quando si esegue questa esercitazione, è necessario aggiornare il file INIT.ORA del database primario in posso che possa ospitare entrambi i ruoli: primario o standby.

	SQL> create pfile from spfile;
	File created.

È poi necessario modificare il pfile per aggiungere i parametri di standby. A tale scopo, aprire il file INITTEST.ORA nel percorso %ORACLE\_HOME%\\database. Aggiungere quindi le istruzioni seguenti al file INITTEST.ORA. Si noti che la convenzione di denominazione per il file INITTEST.ORA è INIT<NomeDatabase>.ORA.

	db_name='TEST'
	db_unique_name='TEST'
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


Il blocco di istruzioni precedente include tre elementi di configurazione importanti:- **LOG\_ARCHIVE\_CONFIG...:** con questa istruzione si definiscono gli ID di database univoci. - **LOG\_ARCHIVE\_DEST\_1...:** con questa istruzione si definisce il percorso della cartella di archivio locale. È consigliabile creare una nuova directory per le esigenze di archiviazione del database e specificare il percorso dell'archivio locale usando l'istruzione in modo esplicito anziché usare la cartella predefinita di Oracle %ORACLE\_HOME%\\database\\archive. - **LOG\_ARCHIVE\_DEST\_2 .... LGWR ASYNC...:** con questa istruzione si definisce un processo writer di log (LGWR) asincrono per raccogliere i dati di ripristino delle transazioni e trasmetterli alle destinazioni di standby. In questo caso, DB\_UNIQUE\_NAME specifica un nome univoco per il database nel server di standby di destinazione.

Quando il nuovo file dei parametri è pronto, è necessario creare il spfile da tale file.

Prima di tutto, arrestare il database:

	SQL> shutdown immediate;

	Database closed.

	Database dismounted.

	ORACLE instance shut down.

Successivamente, eseguire il comando startup nomount come indicato di seguito:

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

Creare ora un spfile:

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

Arrestare quindi il database:

	SQL> shutdown immediate;

	ORA-01507: database not mounted

Usare poi il comando startup per avviare un'istanza:

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##Creare un database di standby fisico
Questa sezione è incentrata sui passaggi da eseguire in Machine2 per preparare il database di standby fisico.

In primo luogo, è necessario connettersi con desktop remoto a Machine2 attraverso il portale di Azure classico.

Nel server di standby (Machine2) creare quindi tutte le cartelle necessarie per il database di standby, ad esempio C:\\<CartellaLocale>\\TEST. Mentre si segue questa esercitazione, assicurarsi che la struttura di cartelle corrisponda alla struttura di cartelle in Machine1 per mantenere tutti i file necessari, ad esempio i file controlfile, datafile, redologfile, udump, bdump e cdump. Definire inoltre le variabili di ambiente ORACLE\_HOME e ORACLE\_BASE in Machine2. In caso contrario, è necessario definirle come variabile di ambiente tramite la finestra di dialogo Variabili d'ambiente. Per accedere a questa finestra di dialogo, avviare l'utilità **Sistema** facendo doppio clic sull'icona Sistema nel **Pannello di controllo**, quindi fare clic sulla scheda **Avanzate** e scegliere **Variabili d'ambiente**. Fare clic sul pulsante **Nuova** in **Variabili di sistema** per impostare le variabili di ambiente. Dopo aver impostato le variabili di ambiente, è necessario chiudere il prompt dei comandi di Windows esistente e aprirne uno nuovo per vedere le modifiche.

Attenersi quindi alla procedura seguente:

1. Preparare un file di parametri di inizializzazione per il database di standby

2. Configurare il listener e tnsnames per supportare il database nelle macchine primaria e di standby

	1. Configurare listener.ora in entrambi i server per contenere le voci per entrambi i database

	2. Configurare tnsnames.ora nelle macchine virtuali primaria e di standby per contenere le voci per i database primario e di standby

	3. Avviare il listener e controllare tnsping in entrambe le macchine virtuali per entrambi i servizi.

3. Avviare l'istanza di standby in stato nomount

4. Usare RMAN per clonare il database e creare un database di standby

5. Avviare il database di standby fisico in modalità di ripristino gestito

6. Verificare il database di standby fisico

### 1\. Preparare un file di parametri di inizializzazione per il database di standby

Questa seziona illustra come preparare un file di parametri di inizializzazione per il database di standby. A tale scopo, copiare innanzitutto il file INITTEST.ORA da Machine1 a Machine2 manualmente. Il file INITTEST.ORA dovrebbe essere ora visibile nella cartella %ORACLE\_HOME%\\database in entrambe le macchine. Modificare quindi il file INITTEST.ORA in Machine2 per configurarlo per il ruolo di standby come specificato di seguito:

	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'


	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


Il blocco di istruzioni precedente include due elementi di configurazione importanti:

-	***. LOG\_ARCHIVE\_DEST\_1:** è necessario creare manualmente la cartella c:\\OracleDatabase\\TEST\_STBY\\archives in Machine2.
-	***.LOG\_ARCHIVE\_DEST\_2:** questo è un passaggio facoltativo. È possibile impostare questo elemento in base alle necessità quando la macchina primaria è in manutenzione e la macchina di standby diventa un database primario.

È quindi necessario avviare l'istanza di standby. Nel server di database di standby, immettere il comando seguente al prompt dei comandi di Windows per creare un'istanza di Oracle creando un nuovo servizio Windows:

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Si noti che il comando **Oradim** crea un'istanza di Oracle, ma non l'avvia È possibile trovarla nella directory C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN.

##Configurare il listener e tnsnames per supportare il database nelle macchine primaria e di standby
Prima di creare un database di standby, è necessario assicurarsi che i database primario e di standby nella configurazione possano comunicare tra loro. A tale scopo, è necessario configurare il listener e TNSNames manualmente o tramite l'utilità di configurazione di rete NETCA. Si tratta di un'attività obbligatoria quando si usa l'utilità Gestione ripristino (RMAN).

### Configurare listener.ora in entrambi i server per contenere le voci per entrambi i database

Connettersi con desktop remoto a Machine1 e modificare il file listener.ora come specificato di seguito. Quando si modifica il file listener.ora, assicurarsi sempre che le parentesi di apertura e chiusura siano allineate nella stessa colonna. È possibile trovare il file listener.ora nella seguente cartella: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

Connettersi quindi con desktop remoto a Machine2 e modificare il file listener.ora file come indicato di seguito: # listener.ora Network Configuration File: C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### Configurare tnsnames.ora nelle macchine virtuali primaria e di standby per contenere le voci per i database primario e di standby

Connettersi con desktop remoto a Machine1 e modificare il file tnsnames.ora come specificato di seguito. È possibile trovare il file tasnames.ora nella seguente cartella: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Connettersi con desktop remoto a Machine2 e modificare il file tnsnames.ora come indicato di seguito:

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### Avviare il listener e controllare tnsping in entrambe le macchine virtuali per entrambi i servizi.

Aprire un nuovo prompt dei comandi di Windows sia nella macchina virtuale primaria che in quella di standby ed eseguire le istruzioni seguenti:

	C:\Users\DBAdmin>tnsping test

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)


	C:\Users\DBAdmin>tnsping test_stby

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##Avviare l'istanza di standby in stato nomount
È necessario configurare l'ambiente per supportare il database di standby nella macchina virtuale di standby (Machine2).

Copiare prima di tutto manualmente il file di password dalla macchina primaria (Machine1) alla macchina di standby (Machine2). Questa operazione è necessaria perché la password per **sys** deve essere la stessa in entrambe le macchine.

Aprire quindi il prompt dei comandi di Windows in Machine2 e impostare le variabili di ambiente in modo che puntino al database di standby, come indicato di seguito:

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

Avviare poi il database di standby in stato nomount e quindi generare un spfile.

Avviare il database:

	SQL>shutdown immediate;

	SQL>startup nomount
	ORACLE instance started.

	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##Usare RMAN per clonare il database e creare un database di standby
È possibile usare l'utilità Gestione ripristino (RMAN) per usare qualsiasi copia di backup del database primario per creare il database di standby fisico.

Connettersi con desktop remoto alla macchina virtuale (Machine2) ed eseguire l'utilità RMAN specificando una stringa di connessione completa sia per l'istanza TARGET (database primario, Machine1) che per l'istanza AUXILLARY (database di standby, Machine2).

>[AZURE.IMPORTANT] Non usare l'autenticazione del sistema operativo perché nella macchina del server di standy non è ancora presente alcun database.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##Avviare il database di standby fisico in modalità di ripristino gestito
In questa esercitazione viene illustrato come creare un database di standby fisico. Per informazioni sulla creazione di un database di standby logico, vedere la documentazione di Oracle.

Aprire il prompt dei comandi di SQL*Plus e abilitare Data Guard nella macchina virtuale di standby o nel server (Machine2) come indicato di seguito:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Quando si apre il database in standby in modalità **MOUNT**, il log shipping dell'archivio continua e il processo di ripristino gestito continua con l'applicazione dei log nel database di standby. Ciò garantisce che il database di standby rimanga aggiornato e allineato al database primario. Si noti che il database di standby non può essere accessibile per attività di creazione di report durante questa fase.

Quando si apre il database di standby in modalità **READ ONLY**, il log shipping dell'archivio continua, ma il processo di ripristino gestito viene interrotto. Ciò causa un disallineamento progressivo del database di standby a causa del mancato aggiornamento fino alla ripresa del processo di ripristino gestito. È possibile accedere al database di standby per la creazione di report durante questo periodo, ma i dati potrebbero non rispecchiare le modifiche più recenti.

In generale, è consigliabile mantenere il database di standby in modalità **MOUNT** per mantenere aggiornati i dati nel database di standby in caso di errore del database primario. È comunque possibile mantenere il database di standby in modalità **READ ONLY** per la creazione di report in base ai requisiti dell'applicazione. I passaggi seguenti illustrano come abilitare Data Guard in modalità di sola lettura tramite SQL*Plus:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##Verificare il database di standby fisico
In questa sezione viene illustrato come verificare la configurazione della disponibilità elevata come amministratore.

Aprire la finestra del prompt dei comandi di SQL*Plus e controllare il log di ripristino archiviato nella macchina virtuale di standby (Machine2):

	SQL> show parameters db_unique_name;

	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY

	SQL> SELECT NAME FROM V$DATABASE

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

Aprire la finestra prompt dei comandi di SQL*Plus ed effettuare il cambio dei file di log nella macchina primaria (Machine1):

	SQL> alter system switch logfile;
	System altered.

	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

Controllare il log di ripristino archiviato nella macchina virtuale di standby (Machine2):

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES

	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

Controllare se sono presenti eventuali gap nella macchina virtuale di standby (Machine2):

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

Un altro metodo di verifica potrebbe consistere nell'eseguire il failover al database di standby e quindi verificare se è possibile il failback al database primario. Per attivare il database di standby come database primario, usare le istruzioni seguenti:

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Se non è stato abilitato il flashback nel database primario originale, è consigliabile eliminare il database primario originale e ricreare un database di standby.

È consigliabile abilitare il flashback nei database primario e di standby. In caso di failover, è possibile eseguire il flashback del database primario all'ora precedente il failover e quindi convertirlo rapidamente in un database in standby.

##Risorse aggiuntive
[Immagini di macchine virtuali Oracle per Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=AcomDC_0128_2016-->