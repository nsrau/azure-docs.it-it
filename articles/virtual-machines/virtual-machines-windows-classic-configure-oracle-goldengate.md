---
title: Configurazione Oracle GoldenGate in macchine virtuali | Microsoft Docs
description: Esercitazione che illustra come configurare e implementare Oracle GoldenGate in macchine virtuali del server Windows di Azure a fini della disponibilità elevata e del ripristino di emergenza.
services: virtual-machines-windows
author: rickstercdn
manager: timlt
documentationcenter: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: rclaus

---
# Configurazione di Oracle GoldenGate per Azure
In questa esercitazione viene illustrato come configurare Oracle GoldenGate in un ambiente con macchine virtuali di Azure a fini della disponibilità elevata e del ripristino di emergenza. L'esercitazione è incentrata sulla [replica bidirezionale](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) per i database di Oracle non RAC e richiede che entrambi i siti siano attivi.

Oracle GoldenGate supporta la distribuzione e l'integrazione dei dati. Consente di configurare una soluzione di distribuzione e sincronizzazione dei dati tramite la configurazione della replica Oracle-Oracle e fornisce una soluzione a disponibilità elevata flessibile. Oracle GoldenGate completa Oracle Data Guard con funzionalità di replica per consentire la distribuzione di informazioni estesa all'intera organizzazione e la possibilità di eseguire aggiornamenti e migrazioni senza tempi di inattività. Per informazioni dettagliate, vedere [Uso di Oracle GoldenGate con Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contiene i seguenti componenti principali: Extract, Data pump, Replicat, file trail o di estrazione, checkpoint, Manager e Collector. Per implementare la replica bidirezionale tra due siti, è necessario creare e avviare tutti i componenti in entrambi i siti. Per informazioni dettagliate sull'architettura di Oracle GoldenGate, vedere la [guida di Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

In questa esercitazione si presuppone che si disponga già di conoscenze teoriche e pratiche sui concetti relativi al ripristino di emergenza e alla disponibilità elevata per il database Oracle, nonché su [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Per altre informazioni, vedere il [sito Web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Inoltre, nell'esercitazione si presuppone che siano già stati implementati i prerequisiti seguenti:

* Lettura della sezione dedicata alle considerazioni sulla disponibilità elevata e il ripristino di emergenza nell'argomento [Immagini di macchine virtuali Oracle - considerazioni varie](virtual-machines-windows-classic-oracle-considerations.md). Si noti che Azure attualmente supporta istanze autonome di Oracle Database, ma non cluster Oracle RAC (Oracle Real Application Cluster).
* Download del software Oracle GoldenGate dal sito Web dei [download Oracle](http://www.oracle.com/us/downloads/index.html). Selezione di Product Pack Oracle Fusion Middleware – Data Integration. Selezione di Oracle GoldenGate su Oracle v11.2.1 Media Pack per Microsoft Windows x64 (64 bit) per un database Oracle 11g. Download di Oracle GoldenGate V11.2.1.0.3 per Oracle 11g 64 bit su Windows 2008 (64 bit).
* Sono state create due macchine virtuali (VM) in Azure usando Oracle Enterprise Edition su Windows Server. Verifica che le macchine virtuali si trovino nello [stesso servizio cloud](virtual-machines-linux-load-balance.md) e nella stessa [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) per assicurare l'accesso reciproco attraverso l'indirizzo IP privato permanente.
* Impostazione dei nomi di macchina virtuale "MachineGG1" per il sito A e "MachineGG2" per il sito B nel portale di Azure classico.
* Creazione dei database di test "TestGG1" nel sito A e "TestGG2" nel sito B.
* Accesso al server Windows come membro del gruppo Administrators o come membro del gruppo **ORA\_DBA**.

In questa esercitazione si apprenderà come:

1. Installare il database nei siti A e B
   
   1. Eseguire il caricamento iniziale dei dati
2. Preparare i siti A e B sito per la replica del database
3. Creare tutti gli oggetti necessari per supportare la replica DDL
4. Configurare GoldenGate Manager nei siti A e B
5. Creare i processi Extract Group e Data Pump nei siti A e B
   
   1. Creare i processi Extract e Data Pump nel sito A
   2. Create una tabella di checkpoint di GoldenGate nel sito B
   3. Aggiungere un processo Replicat nel sito B
   4. Creare i processi Extract e Data Pump nel sito B
   5. Create una tabella di checkpoint di GoldenGate nel sito A
   6. Aggiungere un processo Replicat nel sito A
   7. Eseguire il comando add trandata nei siti A e B
   8. Avviare i processi Extract e Data Pump nel sito A
   9. Avviare i processi Extract e Data Pump nel sito B
   10. Avviare il processo Replicat nel sito A
   11. Avviare il processo Replicat nel sito B
6. Verificare il processo di replica bidirezionale

> [!IMPORTANT]
> Questa esercitazione è stata predisposta e testata con la seguente configurazione software:
> 
> |  | **Database del sito A** | **Database del sito B** |
> | --- | --- | --- |
> | **Versione di Oracle** |Oracle11g versione 2 – (11.2.0.1) |Oracle11g versione 2 – (11.2.0.1) |
> | **Nome del computer** |MachineGG1 |MachineGG2 |
> | **Sistema operativo** |Windows 2008 R2 |Windows 2008 R2 |
> | **SID Oracle** |TESTGG1 |TESTGG2 |
> | **Schema di replica** |SCOTT |SCOTT |
> 
> 

Per le versioni successive di Oracle Database e Oracle GoldenGate potrebbero essere presenti alcune modifiche aggiuntive da implementare. Per informazioni aggiornate specifiche della versione, vedere la documentazione relativa a [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) e [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) nel sito Web di Oracle. Ad esempio, per un database di origine versione 11.2.0.4 e successive, l'acquisizione delle istruzioni DDL viene eseguita dal server logmining in modo asincrono e non richiede l'installazione di trigger, tabelle o altri oggetti di database speciali. Gli aggiornamenti di Oracle GoldenGate possono essere eseguiti senza interrompere le applicazioni utente. L'uso di un trigger DDL e di oggetti di supporto è obbligatorio quando Extract è in modalità integrata con un database di origine Oracle 11g precedente alla versione 11.2.0.4. Per istruzioni dettagliate, vedere la documentazione relativa all'[installazione e configurazione di Oracle GoldenGate per Oracle Database](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

## 1\. Installare il database nei siti A e B
In questa sezione viene illustrato come eseguire i prerequisiti del database nei siti A e B. È necessario eseguire tutti i passaggi di questa sezione in entrambi i siti: sito A e il sito B.

Innanzitutto, connettersi con desktop remoto ai siti A e B tramite il portale di Azure classico. Aprire un prompt dei comandi di Windows e creare una home directory per i file di installazione di Oracle GoldenGate:

    mkdir C:\OracleGG

Quindi, decomprimere e installare il software Oracle GoldenGate in questa cartella. Dopo questo passaggio, è possibile avviare l'interprete dei comandi software di GoldenGate (GGSCI) eseguendo il comando seguente:

    C:\OracleGG\.\ggsci

È possibile usare [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) per eseguire diversi comandi che consentono di configurare, controllare e monitorare Oracle GoldenGate.

Eseguire quindi il comando seguente per creare tutte le sottocartelle dal pacchetto di installazione:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Nel prompt dei comandi di GGSCI eseguire il comando seguente.

    GGSCI (Hostname) 1> EXIT

È quindi è necessario creare un utente del database, che verrà usato dai processi Manager, Extract e Replication di Oracle GoldenGate. Si noti che è possibile creare utenti singoli per ogni processo o configurare un solo utente comune. In questa esercitazione viene creato un solo utente, denominato ggate, al quale verranno concessi i privilegi necessari. Si noti che è necessario eseguire le operazioni seguenti nel sito A e nel sito B.

Aprire la finestra dei comandi di SQL*Plus nel sito A e B sito con privilegi di amministratore di database con **SYSDBA**, ad esempio:

    Enter username: / as sysdba

Quindi eseguire:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

A questo punto individuare il file INIT<SIDDatabase>.ORA nella cartella %ORACLE\_HOME%\\database nei siti A e B e aggiungere i parametri di database seguenti a INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Per un elenco completo di tutti i comandi GGSCI Oracle GoldenGate, vedere le [informazioni di riferimento su Oracle GoldenGate per Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### Eseguire il caricamento iniziale dei dati
È possibile eseguire il caricamento iniziale dei dati nel database seguendo diversi metodi. Ad esempio, è possibile usare il [caricamento diretto di Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) o utilità standard di esportazione e importazione per esportare i dati della tabella dal sito A al sito B.

Per una dimostrazione del processo di replica di Oracle GoldenGate, in questa esercitazione viene illustrato come creare una tabella sia nel sito A che nel sito B usando i comandi seguenti.

Innanzitutto, aprire la finestra dei comandi di SQL*Plus ed eseguire il comando seguente per creare una tabella di inventario per i database dei siti A e B:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Aggiungere quindi un vincolo alla tabella appena creata nei database dei siti A e B:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Concedere quindi tutti i privilegi per la nuova tabella di inventario all'utente ggate nei siti A e B:

    grant all on scott.inventory to ggate;

Successivamente, creare e abilitare un trigger di database, INVENTORY\_CDR\_TRG, nella tabella appena creata per assicurarsi che tutte le transazioni per la nuova tabella vengono registrate se l'utente non è ggate. Effettuare questa operazione nei siti A e B.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


## 2\. Preparare i siti A e B sito per la replica del database
In questa sezione viene illustrato come preparare i siti A e B per la replica del database. È necessario eseguire tutti i passaggi di questa sezione in entrambi i siti: sito A e sito B.

Innanzitutto, connettersi con desktop remoto ai siti A e B tramite il portale di Azure classico. Attivare la modalità archivelog per il database tramite la finestra dei comandi di SQL*Plus:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Abilitare quindi la [registrazione supplementare](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) minima, come indicato di seguito:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Preparare poi il database per il supporto della replica DDL (Data Definition Language):

    SQL> alter system set recyclebin=off scope=spfile;

Infine, arrestare e riavviare il database:

    sql>shutdown immediate
    sql>startup


## 3\. Creare tutti gli oggetti necessari per supportare la replica DDL
In questa sezione sono elencati gli script da usare per creare tutti gli oggetti necessari per supportare la replica DDL. È necessario eseguire gli script specificati in questa sezione sia nel sito A che nel sito B.

Aprire un prompt dei comandi di Windows e passare alla cartella di Oracle GoldenGate, ad esempio C:\\OracleGG. Aprire il prompt dei comandi di SQL*Plus con privilegi di amministratore di database, ad esempio con l'account **SYSDBA**, nei siti A e B.

Eseguire quindi gli script seguenti:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Lo strumento Oracle GoldenGate richiede un account di accesso a livello di tabella per il supporto di DDL (Data Definition Language). Ecco perché è necessario abilitare la registrazione supplementare a livello di tabella tramite il comando ADD TRANDATA. Aprire la finestra dell'interprete dei comandi di Oracle GoldenGate, accedere al database e quindi eseguire il comando ADD TRANDATA:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

## 4\. Configurare GoldenGate Manager nei siti A e B
Oracle GoldenGate Manager esegue una serie di funzioni come avviare gli altri processi di GoldenGate, la gestione dei file di log di trail e la creazione di report.

È necessario configurare il processo Oracle GoldenGate Manager in entrambi i siti. A tale scopo, eseguire le operazioni seguenti nel sito A e nel sito B.

Aprire la finestra dei comandi di Windows e avviare l'interprete dei comandi di Oracle GoldenGate:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Registrare la sessione di GGSCI in un database in modo da poter eseguire i comandi che interessano il database:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Visualizzare lo stato e il ritardo (se pertinente) per tutti i processi Manager, Extract e Replicat in un sistema:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le seguenti informazioni:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Visualizzare lo stato e il ritardo (se pertinente) per tutti i processi Manager, Extract e Replicat in un sistema:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Registrare la sessione di GGSCI in un database in modo da poter eseguire i comandi che interessano il database:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Avviare il processo Manager:

    GGSCI (HostName) 48> start manager
    Manager started.

## 5\. Creare i processi Extract Group e Data Pump nei siti A e B
### Creare i processi Extract e Data Pump nel sito A
È necessario creare i processi Extract e Data Pump nel sito A e nel sito B. Connettersi con desktop remoto ai siti A e B tramite il portale di Azure classico. Aprire la finestra dell'interprete dei comandi GGSCI. Eseguire i comandi seguenti nel sito A:

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le informazioni seguenti: GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml), ON DELETE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml));

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le seguenti informazioni:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

### Create una tabella di checkpoint di GoldenGate nel sito B
È quindi necessario aggiungere una tabella di checkpoint nel sito B. A tale scopo, apri una finestra dell'interprete dei comandi di GoldenGate ed eseguire:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Aggiungere quindi la tabella di checkpoint al database, dove ggate è il proprietario:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Aggiungere il nome della tabella di checkpoint al file GLOBALS nel server di destinazione, ovvero il sito B in questo passaggio. Modificare il file GLOBALS nel sito B:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Aggiungere quindi il parametro CHECKPOINTTABLE al file GLOBALS esistente:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Come passaggio finale, salvare e chiudere il file dei parametri GLOBALS.

### Aggiungere un processo Replicat nel sito B
In questa sezione viene descritto come aggiungere un processo REPLICAT "REP2" nel sito B.

Usare il comando ADD REPLICAT per creare un gruppo Replicat nel sito B:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le seguenti informazioni:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### Creare i processi Extract e Data Pump nel sito B
In questa sezione viene descritto come creare un nuovo processo Extract denominato "EXT2" e un nuovo processo Data Pump "DPUMP2" nel sito B:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le seguenti informazioni:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le seguenti informazioni:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

### Create una tabella di checkpoint di GoldenGate nel sito A
Aprire la finestra dell'interprete dei comandi di Oracle GoldenGate e creare una tabella di checkpoint:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

È inoltre necessario aggiungere il nome della tabella di checkpoint al file GLOBALS nel sito A.

Aprire la finestra dell'interprete dei comandi di Oracle GoldenGate e modificare il file GLOBALS nel sito A:

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Salvare e chiudere il file dei parametri GLOBALS.

### Aggiungere un processo Replicat nel sito A
In questa sezione viene descritto come aggiungere un processo Replicat "REP1" nel sito A.

Il comando seguente crea un gruppo Replicat rep1 con il nome di un trail e la tabella di checkpoint associata.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Aprire il file dei parametri con il comando EDIT PARAMS e quindi aggiungere le seguenti informazioni:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### Eseguire il comando add trandata nei siti A e B
Abilitare la registrazione supplementare a livello di tabella usando il comando ADD TRANDATA. Aprire la finestra dell'interprete dei comandi di Oracle GoldenGate, accedere al database e quindi eseguire il comando ADD TRANDATA.

Connettersi con desktop remoto a MachineGG1, aprire l'interprete dei comandi di Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Connettersi con desktop remoto a MachineGG2, aprire l'interprete dei comandi di Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Visualizzare informazioni sullo stato della registrazione supplementare a livello di tabella:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

### Eseguire il comando add trandata nei siti A e B
Abilitare la registrazione supplementare a livello di tabella usando il comando ADD TRANDATA. Aprire la finestra dell'interprete dei comandi di Oracle GoldenGate, accedere al database e quindi eseguire il comando ADD TRANDATA.

Connettersi con desktop remoto a MachineGG1, aprire l'interprete dei comandi di Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Connettersi con desktop remoto a MachineGG2, aprire l'interprete dei comandi di Oracle GoldenGate ed eseguire:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

### Avviare i processi Extract e Data Pump nel sito A
Avviare il processo Extract ext1 nel sito A:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Avviare il processo Data Pump dpump1 nel sito A:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Visualizzare informazioni sul gruppo Extract ext1: GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Last Started 2013-11-25 08:03 Status RUNNING Checkpoint Lag 00:00:00 (updated 00:00:02 ago) Log Read Checkpoint Oracle Redo Logs 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Visualizzare lo stato e il ritardo (se pertinente) per tutti i processi Manager, Extract e Replicat in un sistema:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

### Avviare i processi Extract e Data Pump nel sito B
Avviare il processo Extract ext2 nel sito B:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Avviare il processo Data Pump dpump2 nel sito B:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Visualizzare lo stato e il ritardo (se pertinente) per tutti i processi Manager, Extract e Replicat in un sistema:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### Avviare il processo Replicat nel sito A
In questa sezione viene descritto come avviare il processo Replicat "REP1" nel sito A.

Avviare il processo Replicat nel sito A:

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Visualizzare lo stato di un gruppo Replicat:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

### Avviare il processo Replicat nel sito B
In questa sezione viene descritto come avviare il processo Replicat "REP2" nel sito B.

Avviare il processo Replicat nel sito B:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Visualizzare lo stato di un gruppo Replicat:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

## 6\. Verificare il processo di replica bidirezionale
Per verificare la configurazione di Oracle GoldenGate, inserire una riga nel database nel sito A. Connettersi con desktop remoto al sito A. Aprire la finestra dei comandi di SQL*Plus ed eseguire: SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Controllare quindi se tale riga viene replicata nel sito B. A tale scopo, connettersi con desktop remoto al sito B. Aprire la finestra di SQL*Plus ed eseguire:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Inserire un nuovo record nel sito B:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Connettersi con desktop remoto al sito A e controllare se la replica ha avuto luogo:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

## Risorse aggiuntive
[Immagini di macchine virtuali Oracle per Azure](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0914_2016-->