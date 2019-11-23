---
title: Installare TmaxSoft OpenFrame in macchine virtuali di Azure
description: Riospitare i carichi di lavoro del mainframe IBM z/OS usando l'ambiente OpenFrame di TmaxSoft in macchine virtuali di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436055"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installare TmaxSoft OpenFrame in Azure

Informazioni su come configurare un ambiente OpenFrame in Azure adatto per lo sviluppo, le demo, i test o i carichi di lavoro di produzione. Questa esercitazione illustra ogni passaggio.

OpenFrame include più componenti che creano l'ambiente di emulazione mainframe in Azure. Ad esempio, OpenFrame Servizi online sostituire il middleware mainframe, ad esempio IBM Customer Information Control System (CICS) e OpenFrame batch, con il relativo componente TJES, sostituisce il sottosistema di immissione dei processi (JES) del mainframe IBM.

OpenFrame funziona con qualsiasi database relazionale, tra cui Oracle Database, Microsoft SQL Server, IBM DB2 e MySQL. Questa installazione di OpenFrame usa il database relazionale TmaxSoft tibero. Sia OpenFrame che tibero vengono eseguiti in un sistema operativo Linux. Questa esercitazione installa CentOS 7,3, sebbene sia possibile usare altre distribuzioni Linux supportate. Il server applicazioni OpenFrame e il database tibero sono installati in una macchina virtuale (VM).

L'esercitazione illustra l'installazione dei componenti di OpenFrame suite. Alcune devono essere installate separatamente.

Componenti principali di OpenFrame:

- Pacchetti di installazione necessari.
- Database tibero.
- Open Database Connectivity (ODBC) viene usato dalle applicazioni in OpenFrame per comunicare con il database di Tiberi.
- OpenFrame base, il middleware che gestisce l'intero sistema.
- Batch OpenFrame, la soluzione che sostituisce i sistemi batch del mainframe.
- TACF, un modulo del servizio che controlla l'accesso degli utenti a sistemi e risorse.
- Prosort, uno strumento di ordinamento per le transazioni batch.
- OFCOBOL, un compilatore che interpreta i programmi COBOL del mainframe.
- OFASM, un compilatore che interpreta i programmi assembler del mainframe.
- OpenFrame Server Type C (OSC), la soluzione che sostituisce il middleware del mainframe e IBM CICS.
- Java Enterprise User Solution (JEUS), un server applicazioni web certificato per Java Enterprise Edition 6.
- OFGW, il componente gateway OpenFrame che fornisce un listener 3270.
- OFManager, una soluzione che fornisce funzioni operative e di gestione di OpenFrame nell'ambiente Web.

Altri componenti di OpenFrame necessari:

- OSI, la soluzione che sostituisce il middleware mainframe e il controller di dominio IMS.
- TJES, la soluzione che fornisce l'ambiente JES del mainframe.
- OFTSAM, la soluzione che consente di usare i file SAM (V) nel sistema aperto.
- OFHiDB, la soluzione che sostituisce il database IMS del mainframe.
- OFPLI, un compilatore che interpreta i programmi PL/I del mainframe.
- PROTRIEVE, una soluzione che esegue la lingua del mainframe CA-Easytrieve.
- OFMiner, una soluzione che analizza gli asset mainframe e li esegue quindi la migrazione in Azure.

## <a name="architecture"></a>Architettura

La figura seguente fornisce una panoramica dei componenti dell'architettura OpenFrame 7,0 installati in questa esercitazione:

![Componenti di OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisiti di sistema di Azure

La tabella seguente elenca i requisiti per l'installazione in Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>Descrizione</th></tr>
</thead>
<tbody>
<tr><td>Distribuzioni Linux supportate in Azure
</td>
<td>
Linux x86 2,6 (32 bit, 64 bit)<br/>
Red Hat 7. x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Core: 2 (minimo)<br/>
Memoria: 4 GB (minimo)<br/>
Spazio di swapping: 1 GB (minimo)<br/>
Disco rigido: 100 GB (minimo)<br/>
</td>
</tr>
<tr><td>Software facoltativo per utenti Windows
</td>
<td>PuTTy: usato in questa guida per configurare le funzionalità della VM<br/>
WinSCP: un client SFTP e un client FTP diffusi che è possibile usare<br/>
Eclipse per Windows: piattaforma di sviluppo supportata da TmaxSoft<br/>
(Microsoft Visual Studio non è supportato in questo momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>prerequisiti

Pianifica la spesa per alcuni giorni per assemblare tutto il software necessario e completare tutti i processi manuali.

Prima di iniziare, eseguire le operazioni seguenti:

- Ottenere il supporto di installazione di OpenFrame da TmaxSoft. Se si è un cliente di TmaxSoft esistente, contattare il rappresentante di TmaxSoft per una copia con licenza. In caso contrario, richiedere una versione di valutazione di [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Richiedere la documentazione di OpenFrame inviando un messaggio di posta elettronica a <support@tmaxsoft.com>.

- Ottenere una sottoscrizione di Azure, se non è già presente. È anche possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Facoltativa. Configurare un tunnel VPN da sito a sito o un JumpBox per limitare l'accesso alla macchina virtuale di Azure agli utenti autorizzati dell'organizzazione. Questo passaggio non è obbligatorio, ma è una procedura consigliata.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurare una macchina virtuale in Azure per OpenFrame e tibero

È possibile configurare l'ambiente OpenFrame usando diversi modelli di distribuzione, ma la procedura seguente mostra come distribuire il server applicazioni OpenFrame e il database di tibero in una VM. In ambienti più grandi e per carichi di lavoro di dimensioni elevate, una procedura consigliata consiste nel distribuire il database separatamente nella propria macchina virtuale per ottenere prestazioni migliori.

**Per creare una macchina virtuale**

1. Passare alla portale di Azure in <https://portal.azure.com> e accedere al proprio account.

2. Fare clic su **Virtual machines**.

    ![Elenco di risorse in portale di Azure](media/vm-01.png)

3. Fare clic su **Aggiungi**.

    ![Aggiungi opzione in portale di Azure](media/vm-02.png)

4. A destra dei **sistemi operativi**, fare clic su **altro**.

     ![Altre opzioni in portale di Azure](media/vm-03.png)

5. Fare clic su **CentOS 7,3** per seguire esattamente questa procedura dettagliata oppure è possibile scegliere un'altra distribuzione Linux supportata.

     ![Opzioni del sistema operativo in portale di Azure](media/vm-04.png)

6. Nelle impostazioni di **base** immettere **nome**, **nome utente**, tipo di **autenticazione**, **sottoscrizione** (con pagamento in base al consumo è lo stile di pagamento di AWS) e **gruppo di risorse** (usare uno esistente o creare un gruppo TmaxSoft).

7. Al termine, inclusa la coppia di chiavi pubblica/privata per il **tipo di autenticazione**, fare clic su **Invia**.

> [!NOTE]
> Se si usa una chiave pubblica SSH per il **tipo di autenticazione**, vedere i passaggi nella sezione successiva per generare la coppia di chiavi pubblica/privata, quindi riprendere i passaggi qui.

### <a name="generate-a-publicprivate-key-pair"></a>Genera una coppia di chiavi pubblica/privata

Se si usa un sistema operativo Windows, è necessario PuTTYgen per generare una coppia di chiavi pubblica/privata.

La chiave pubblica può essere condivisa liberamente, ma la chiave privata deve essere mantenuta interamente segreta e non deve mai essere condivisa con un'altra parte. Dopo la generazione delle chiavi, è necessario incollare la **chiave pubblica SSH** nella configurazione, ovvero caricarla nella VM Linux. Viene archiviato all'interno di chiavi di\_autorizzate all'interno della directory \~/.ssh della Home directory dell'account utente. La VM Linux è quindi in grado di riconoscere e convalidare la connessione una volta fornita la **chiave privata SSH** associata nel client SSH (in questo caso, Putty).

Quando si assegnano nuovi utenti ad accedere alla macchina virtuale: 

- Ogni nuovo singolo genera le proprie chiavi pubbliche/private usando PuTTYgen.
- Gli utenti archiviano separatamente le proprie chiavi private e inviano le informazioni sulla chiave pubblica all'amministratore della macchina virtuale.
- L'amministratore incolla il contenuto della chiave pubblica nel \~file di chiavi di\_/.ssh/Authorized.
- Il nuovo utente si connette tramite PuTTy.

**Per generare una coppia di chiavi pubblica/privata**

1.  Scaricare PuTTYgen da <https://www.putty.org/> e installarlo usando tutte le impostazioni predefinite.

2.  Per aprire PuTTYgen, individuare la directory di installazione di PuTTy in C:\\Program Files\\PuTTy.

    ![Interfaccia PuTTy](media/puttygen-01.png)

3.  Fare clic su **Genera**.

    ![Finestra di dialogo Generatore di chiavi PuTTy](media/puttygen-02.png)

4.  Dopo la generazione, salvare sia la chiave pubblica che la chiave privata. Incollare il contenuto della chiave pubblica nella sezione **chiave pubblica SSH** del riquadro **crea macchina virtuale \> nozioni di base** (illustrato nei passaggi 6 e 7 della sezione precedente).

    ![Finestra di dialogo Generatore di chiavi PuTTy](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurare le funzionalità della VM

1. Nel pannello **Scegli una dimensione** in portale di Azure scegliere le impostazioni hardware del computer Linux desiderate. I requisiti *minimi* per installare sia tibero che OpenFrame sono 2 CPU e 4 GB di RAM, come illustrato in questo esempio di installazione:

    ![Creazione di una macchina virtuale-nozioni di base](media/create-vm-01.png)

2. Fare clic su **3 impostazioni** e utilizzare le impostazioni predefinite per configurare le funzionalità facoltative.
3. Esaminare i dettagli di pagamento.

    ![Crea macchina virtuale-acquisto](media/create-vm-02.png)

4. Inviare le selezioni. Azure inizia a distribuire la macchina virtuale. Questo processo richiede in genere alcuni minuti.

5. Quando la macchina virtuale viene distribuita, viene visualizzato il relativo dashboard, che Mostra tutte le impostazioni selezionate durante la configurazione. Prendere nota dell' **indirizzo IP pubblico**.

    ![TMAX nel dashboard di Azure](media/create-vm-03.png)

6. Aprire PuTTY.

7. Per **nome host**Digitare il nome utente e l'indirizzo IP pubblico copiato. Ad esempio, **username\@IP pubblico**.

    ![Finestra di dialogo configurazione PuTTy](media/putty-01.png)

8. Nella casella **categoria** fare clic su **connessione \> SSH \> auth**. Consente di specificare il percorso del file di **chiave privata** .

    ![Finestra di dialogo configurazione PuTTy](media/putty-02.png)

9. Fare clic su **Apri** per avviare la finestra Putty. Se l'operazione ha esito positivo, si è connessi alla nuova VM CentOS in esecuzione in Azure.

10. Per accedere come utente root, digitare **sudo bash**.

    ![Accesso utente root nella finestra di comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurare l'ambiente e i pacchetti

Ora che la macchina virtuale è stata creata ed è stato effettuato l'accesso, è necessario eseguire alcuni passaggi di installazione e installare i pacchetti di preinstallazione necessari.

1. Eseguire il mapping del nome **ofdemo** all'indirizzo IP locale usando vi per modificare il file hosts (`vi /etc/hosts`). Supponendo che l'indirizzo IP sia 192.168.96.148 ofdemo, questo è prima della modifica:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Questa operazione è successiva alla modifica:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Creare gruppi e utenti:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Modificare la password per l'utente oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Aggiornare i parametri del kernel in/etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Aggiornare i parametri del kernel in modo dinamico senza riavvio:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Ottenere i pacchetti necessari: verificare che il server sia connesso a Internet, scaricare i pacchetti seguenti e quindi installarli:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - Libaio
     - ncurses

          > [!NOTE]
          > Dopo aver installato il pacchetto ncurses, creare i collegamenti simbolici seguenti:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c + +
     - libaio-devel.x86\_64
     - STrace
     - ltrace
     - gdb

7. In caso di installazione di Java RPM, procedere come segue:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Installare il database tibero

Tibero fornisce le numerose funzioni chiave nell'ambiente OpenFrame in Azure:

- Tibero viene usato come archivio dati interno di OpenFrame per varie funzioni di sistema.
- I file VSAM, inclusi KSDS, RRDS e ESDS, usano il database tibero internamente per l'archiviazione dei dati.
- Il repository di dati TACF è archiviato in tibero.
- Le informazioni sul catalogo di OpenFrame vengono archiviate in tibero.
- Il database tibero può essere usato come sostituto per IBM DB2 per archiviare i dati dell'applicazione.

**Per installare tibero**

1. Verificare che il file del programma di installazione binario di tibero sia presente ed esaminare il numero di versione.
2. Copiare il software tibero nell'account utente di tibero (oFrame). Ad esempio:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Aprire. bash\_profile in vi (`vi .bash_profile`) e incollare quanto segue:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Per eseguire il profilo bash, al prompt dei comandi digitare:

    ```
    source .bash_profile
    ```

5. Generare il file TIP (un file di configurazione per tibero), quindi aprirlo in vi. Ad esempio:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modificare \$TB\_HOME/client/config/tbdsn. TBR e inserire 127.0.0.1 invece oflocalhost come illustrato di seguito:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Creare il database. Viene visualizzato l'output seguente:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Per riciclare tibero, arrestarlo prima di tutto con il comando `tbdown`. Ad esempio:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. A questo punto, avviare tibero usando `tbboot`. Ad esempio:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Per creare uno spazio di tabella, accedere al database usando SYS User (sys/tmax), quindi creare lo spazio di tabella necessario per il volume predefinito e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Digitare ora i comandi SQL seguenti:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Avviare tibero e verificare che i processi di tibero siano in esecuzione:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Output:

![Output di tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Installare ODBC

Le applicazioni in OpenFrame comunicano con il database tibero usando l'API ODBC fornita dal progetto unixODBC open source.

Per installare ODBC:

1. Verificare che sia presente il file del programma di installazione unixODBC-2.3.4. tar. gz o usare il comando `wget unixODBC-2.3.4.tar.gz`. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Decomprimere il file binario. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Passare alla directory unixODBC-2.3.4 e generare il makefile usando le informazioni sul controllo del computer. Ad esempio:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Per impostazione predefinita, unixODBC viene installato in/usr/local, quindi `--prefix` passa un valore per modificare il percorso. Analogamente, i file di configurazione vengono installati in/etc per impostazione predefinita, quindi `--sysconfdir` passa il valore della posizione desiderata.

4. Esegui Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copiare il file eseguibile nella directory del programma dopo la compilazione. Ad esempio:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Usare vi per modificare il profilo bash (`vi ~/.bash_profile`) e aggiungere quanto segue:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Applicare ODBC. Modificare di conseguenza i file seguenti. Ad esempio:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Creare un collegamento simbolico e convalidare la connessione al database tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Viene visualizzato l'output seguente:

![Output ODBC che mostra la connessione a SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installare OpenFrame base

Il server applicazioni di base viene installato prima dei singoli servizi usati da OpenFrame per gestire il sistema in Azure, inclusi i processi del server di gestione delle transazioni.

**Per installare OpenFrame base**

1. Assicurarsi che l'installazione di tibero abbia avuto esito positivo, quindi verificare che siano presenti i seguenti OpenFrame\_Base7\_0\_Linux\_x86\_64. bin Installer e il file di configurazione base. Properties.

2. Aggiornare il profilo bash con le informazioni specifiche di Tiberi seguenti:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Eseguire il profilo bash:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Verificare che i processi di tibero siano in esecuzione. Ad esempio:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Assicurarsi di avviare tibero prima dell'installazione.

5. Generare la licenza in [TechNet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e inserire le licenze OpenFrame base, batch, TACF, OSC nella cartella appropriata:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Scaricare i file binary di base OpenFrame e base. Properties:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Eseguire il programma di installazione utilizzando il file base. Properties. Ad esempio:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Al termine, viene riprodotto il messaggio Installazione completata.

8. Verificare la struttura della directory di base OpenFrame usando il comando `ls -ltr`. Ad esempio:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Inizio OpenFrame base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![output del comando tmboot](media/base-02.png)

10. Verificare che lo stato del processo sia pronto usando il comando tmadmin in si. RDY viene visualizzato nella colonna **stato** per ognuno dei processi:

     ![output del comando tmadmin](media/base-03.png)

11. Arresta OpenFrame base:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Installare OpenFrame batch

OpenFrame batch è costituito da diversi componenti che simulano gli ambienti batch mainframe e vengono usati per eseguire processi batch in Azure.

**Per installare il batch**

1. Verificare che l'installazione di base sia stata completata, quindi verificare che il file di configurazione OpenFrame\_Batch7\_0\_fix2\_MVS\_Linux\_x86\_64. bin Installer e batch. Properties siano presenti:

2. Al prompt dei comandi digitare `vi batch.properties` per modificare il file batch. Properties utilizzando vi.

3. Modificare i parametri come indicato di seguito:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Per eseguire il programma di installazione batch, al prompt dei comandi digitare:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Al termine dell'installazione, avviare i gruppi di OpenFrame installati digitando `tmboot` al prompt dei comandi.

    ![output tmboot](media/tmboot-01.png)

6. Digitare `tmadmin` al prompt dei comandi per verificare il processo OpenFrame.

    ![Schermata di amministrazione di tmax](media/tmadmin-01.png)

7. Eseguire i comandi seguenti:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Usare il comando `tmdown` per avviare e arrestare batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Installare TACF

TACF Manager è un modulo del servizio OpenFrame che controlla l'accesso degli utenti a sistemi e risorse attraverso la protezione RACF.

**Per installare TACF**

1. Verificare che siano presenti i file di configurazione OpenFrame\_Tacf7\_0\_fix2\_Linux\_x86\_64. bin e TACF. Properties.
2. Verificare che l'installazione batch sia stata completata, quindi usare vi per aprire il file TACF. Properties (`vi tacf.properties`).
3. Modificare i parametri di TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Dopo aver completato il programma di installazione di TACF, applicare le variabili di ambiente TACF. Al prompt dei comandi digitare:

     ```
     source \~/.bash\_profile
     ```

5. Eseguire il programma di installazione di TACF. Al prompt dei comandi digitare:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     L'output ha un aspetto simile al seguente:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Al prompt dei comandi digitare `tmboot` per riavviare OpenFrame. L'output ha un aspetto simile al seguente:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Verificare che lo stato del processo sia pronto usando `tmadmin` nel comando `si`. Ad esempio:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Nella colonna **stato** viene visualizzato RDY:

    ![RDY nella colonna stato](media/tmboot-02.png)

8. Eseguire i comandi seguenti:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Arrestare il server utilizzando il comando `tmdown`. L'output ha un aspetto simile al seguente:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Installa prosort

Prosort è un'utilità utilizzata nelle transazioni batch per l'ordinamento dei dati.

**Per installare prosort**

1. Verificare che l'installazione batch sia stata completata correttamente e quindi verificare che sia presente il file del programma di installazione **prosort-bin-prosort\_2sp3-linux64-2123-opt. tar. gz** .

2. Eseguire il programma di installazione utilizzando il file delle proprietà. Al prompt dei comandi digitare:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Spostare la directory di prosorte nel percorso Home. Al prompt dei comandi digitare:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Creare una sottodirectory di licenza e copiarvi il file di licenza. Ad esempio:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Aprire bash. profile in vi (`vi .bash_profile`) e aggiornarlo come segue:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Per eseguire il profilo bash, al prompt dei comandi digitare: `. .bash_profile`

7. Creare il file di configurazione. Ad esempio:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Creare il collegamento simbolico. Ad esempio:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verificare l'installazione di prosorte eseguendo il comando `prosort -h`. Ad esempio:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Installare OFCOBOL

OFCOBOL è il compilatore OpenFrame che interpreta i programmi COBOL del mainframe. 

**Per installare OFCOBOL**

1. Assicurarsi che l'installazione batch/online sia stata completata correttamente, quindi verificare che sia presente il file OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64. bin Installer.

2. Per eseguire il programma di installazione di OFCOBOL, al prompt dei comandi digitare:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leggere il contratto di licenza e premere INVIO per continuare.

4. Accettare il contratto di licenza. Al termine dell'installazione, viene visualizzato quanto segue:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Aprire il profilo bash in vi (`vi .bash_profile`) e verificare che sia aggiornato con le variabili OFCOBOL.
6. Eseguire il profilo bash. Al prompt dei comandi digitare:

     ```
      source ~/.bash_profile
     ```

7. Copiare la licenza OFCOBOL nella cartella installata. Ad esempio:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Passare al file di configurazione OpenFrame tjclrun. conf e aprirlo in vi. Ad esempio:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Di seguito è illustrata la sezione SYSLIB prima della modifica:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Di seguito è illustrata la sezione SYSLIB dopo la modifica:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Esaminare il file OpenFrame\_COBOL\_InstallLog. log in vi e verificare che non siano presenti errori. Ad esempio:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Usare il comando `ofcob --version` ed esaminare il numero di versione per verificare l'installazione. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Riavviare OpenFrame usando il comando `tmdown/tmboot`.

## <a name="install-ofasm"></a>Installare OFASM

OFASM è il compilatore OpenFrame che interpreta i programmi assembler del mainframe.

**Per installare OFASM**

1. Assicurarsi che l'installazione batch/online sia stata completata correttamente, quindi verificare che sia presente il file OpenFrame\_ASM3\_0\_Linux\_x86\_64. bin Installer.

2. Eseguire il programma di installazione. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leggere il contratto di licenza e premere INVIO per continuare.
4. Accettare il contratto di licenza.
5. Verificare che il profilo bash venga aggiornato con le variabili OFASM. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Aprire il file di configurazione OpenFrame tjclrun. conf in vi e modificarlo come segue:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Di seguito è illustrata la sezione [SYSLIB] *prima* della modifica:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Di seguito è illustrata la sezione [SYSLIB] *dopo* la modifica:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Aprire il file OpenFrame\_ASM\_InstallLog. log in vi e verificare che non siano presenti errori. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Riavviare OpenFrame eseguendo uno dei comandi seguenti:

     ```
     tmdown / tmboot
     ```

     o

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installare OSC

OSC è l'ambiente OpenFrame simile a IBM CICS che supporta le transazioni OLTP ad alta velocità e altre funzioni di gestione.

**Per installare OSC**

1. Verificare che l'installazione di base sia stata completata, quindi verificare che siano presenti il file di configurazione OpenFrame\_OSC7\_0\_fix2\_Linux\_x86\_64. bin e OSC. Properties.
2. Modificare i parametri seguenti nel file osc. Properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Eseguire il programma di installazione usando il file delle proprietà, come illustrato di seguito:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Al termine, viene visualizzato il messaggio "installazione completata".

4. Verificare che il profilo bash venga aggiornato con le variabili OSC.
5. Esaminare il file OpenFrame\_OSC7\_0\_fix2\_InstallLog. log. Dovrebbe essere visualizzata una schermata analoga alla seguente:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Usare vi per aprire il file di configurazione ofsys. seq. Ad esempio:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Nelle sezioni \#BASE e \#BATCH modificare i parametri come illustrato.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Copiare il file di licenza. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Per avviare e arrestare OSC, inizializzare la memoria condivisa dell'area CICS digitando `osctdlinit OSCOIVP1` al prompt dei comandi.

10. Eseguire `oscboot` per avviare OSC. L'output ha un aspetto simile al seguente:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Per verificare che lo stato del processo sia pronto, utilizzare il comando `tmadmin` in si. Tutti i processi devono visualizzare RDY nella colonna **stato** .

    ![Processi che visualizzano RDY](media/tmadmin-02.png)

12. Arrestare OSC usando il comando `oscdown`.

## <a name="install-jeus"></a>Installare JEUS

JEUS (Java Enterprise User Solution) fornisce il livello di presentazione del server applicazioni Web OpenFrame.

Prima di installare JEUS, installare il pacchetto di Apache Ant, che fornisce le librerie e gli strumenti da riga di comando necessari per installare JEUS.

**Per installare Apache Ant**

1. Scaricare il file binario Ant usando il comando `wget`. Ad esempio:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Utilizzare l'utilità `tar` per estrarre il file binario e spostarlo in una posizione appropriata. Ad esempio:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Per migliorare l'efficienza, creare un collegamento simbolico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Aprire il profilo bash in vi (`vi .bash_profile`) e aggiornarlo con le variabili seguenti:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Applicare la variabile di ambiente modificata. Ad esempio:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Per installare JEUS**

1. Espandere il programma di installazione utilizzando l'utilità `tar`. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Creare una cartella **jeus** (`mkdir jeus7`) e decomprimere il file binario.
3. Passare alla directory di **installazione** (oppure usare il parametro JEUS per il proprio ambiente). Ad esempio:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Eseguire `ant clean-all` prima di eseguire la compilazione. L'output ha un aspetto simile al seguente:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Eseguire un backup del file Domain-config-template. Properties. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Aprire il file Domain-config-template. Properties in vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Modificare `jeus.password=jeusadmin nodename=Tmaxsoft` in `jeus.password=tmax1234 nodename=ofdemo`

8. Eseguire il comando `ant install` per compilare JEUS.
9.  Aggiornare il file del profilo\_. bash con le variabili JEUS, come illustrato di seguito:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Eseguire il profilo bash. Ad esempio:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Facoltativo*. Creare un alias per l'arresto e l'avvio facili dei componenti JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Per verificare l'installazione, avviare il server di amministrazione di dominio come illustrato:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verificare tramite accesso Web usando la sintassi:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Ad esempio, <http://192.168.92.133:9736/webadmin/login.> viene visualizzata la schermata di accesso:
    
     ![Schermata di accesso di JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Se si verificano problemi con la sicurezza della porta, aprire la porta 9736 o disabilitare il firewall (`systemctl stop firewall`).

14. Per modificare il nome host per Server1, fare clic su **blocca & modifica**, quindi fare clic su **Server1**. Nella finestra Server modificare il nome host nel modo seguente:

    1.  Modificare **NodeName** in **ofdemo**.
    2.  Fare clic su **OK** sul lato destro della finestra.
    3.  Fare clic su **Applica modifiche** nella parte inferiore sinistra della finestra e, per descrizione, immettere *nome host*.

    ![Schermata di WebAdmin JEUS](media/jeus-02.png)

15. Verificare che la configurazione sia stata completata correttamente nella schermata di conferma.

    ![schermata del server jeus_domain](media/jeus-03.png)

16. Avviare il processo del server gestito "server1" usando il comando seguente:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installare OFGW

OFGW è il gateway OpenFrame che supporta la comunicazione tra l'emulatore di terminale 3270 e la base OSI e gestisce le sessioni tra l'emulatore di terminale e l'OSI.

**Per installare OFGW**

1. Assicurarsi che JEUS sia stato installato correttamente, quindi verificare che sia presente il file del programma di installazione OFGW7\_0\_1\_Generic. bin.
2. Eseguire il programma di installazione. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Usare i percorsi seguenti per i prompt corrispondenti:
     -   Home directory di JEUS
     -   Nome di dominio JEUS
     -   Nome del server JEUS
     -   Driver tibero
     -   ID nodo tmax ofdemo

4. Accettare le altre impostazioni predefinite, quindi premere INVIO per uscire dal programma di installazione.

5. Verificare che l'URL per OFGW funzioni come previsto:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Viene visualizzata la schermata seguente:

    ![OpenFrame webterminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installare OFManager

OFManager fornisce funzioni operative e di gestione per OpenFrame nell'ambiente Web.

**Per installare OFManager**

1. Verificare che sia presente il file del programma di installazione OFManager7\_Generic. bin.
2. Eseguire il programma di installazione. Ad esempio:

     ```
     OFManager7_Generic.bin
     ```

3.  Premere INVIO per continuare e quindi accettare il contratto di licenza.
4.  Scegliere la cartella di installazione.
5.  Accettare i valori predefiniti.
6.  Scegliere tibero come database.
7.  Premere INVIO per uscire dal programma di installazione.
8.  Verificare che l'URL per OFManager funzioni come previsto:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Viene visualizzata la schermata Start:

![Schermata di accesso di TMAX OpenFrame Manager](media/ofmanager-01.png)

Questa operazione completa l'installazione dei componenti di OpenFrame.

## <a name="next-steps"></a>Passaggi successivi

Se si sta prendendo in considerazione una migrazione del mainframe, l'ecosistema di partner in espansione è disponibile per aiutare l'utente. Per indicazioni dettagliate sulla scelta di una soluzione dei partner, vedere [Platform Modernization Alliance](https://datamigration.microsoft.com/).

-   [Inizia a usare Azure](https://docs.microsoft.com/azure/)
-   [Documentazione di Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guida Lift-and-Shift del data center virtuale di Azure](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
