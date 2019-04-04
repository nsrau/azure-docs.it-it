---
title: Installare TmaxSoft OpenFrame in macchine virtuali di Azure
description: Rehosting IBM z/OS mainframe carichi di lavoro con ambiente TmaxSoft OpenFrame in macchine virtuali (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 6b109f347ee7a917b57acfc56ab4418755295bc5
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896373"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installare TmaxSoft OpenFrame in Azure

Informazioni su come configurare un ambiente OpenFrame in Azure adatto per lo sviluppo, demo, test o i carichi di lavoro di produzione. Questa esercitazione illustra ogni passaggio.

OpenFrame include più componenti che crea l'ambiente di emulazione di mainframe in Azure. Ad esempio, servizi online OpenFrame sostituire il middleware di mainframe, ad esempio IBM Customer informazioni Control System (CICS) e OpenFrame Batch, con il componente relativo TJES, sostituisce processo voce di sottosistema (JES del mainframe IBM).

OpenFrame funziona con qualsiasi database relazionale, tra cui Oracle Database Microsoft SQL Server, IBM Db2 e MySQL. Questa installazione di OpenFrame Usa il database relazionale TmaxSoft Tibero. OpenFrame sia Tibero eseguiti in un sistema operativo Linux. Questa esercitazione installa CentOS 7.3, sebbene sia possibile usare altre distribuzioni Linux supportate. Il server applicazioni OpenFrame e il database Tibero vengono installati in una macchina virtuale (VM).

Nell'esercitazione viene descritta l'installazione dei componenti OpenFrame suite. Alcuni devono essere installati separatamente.

Componenti OpenFrame Main:

- Pacchetti di installazione richiesti.
- Database Tibero.
- Open Database Connectivity (ODBC) viene utilizzato dalle applicazioni in OpenFrame per comunicare con il database Tibero.
- Valore di OpenFrame Base, il middleware che gestisce l'intero sistema.
- OpenFrame Batch, la soluzione che sostituisce i sistemi di batch del mainframe.
- TACF, un modulo del servizio che controlla l'accesso utente ai sistemi e alle risorse.
- ProSort, uno strumento di ordinamento per le transazioni di batch.
- OFCOBOL, un compilatore che interpreta i programmi COBOL del mainframe.
- OFASM, un compilatore che interpreta i programmi di assembler del mainframe.
- OpenFrame Server tipo C (Oschooser), la soluzione che sostituisce middleware al mainframe e IBM CICS.
- Java Enterprise utente soluzione (JEUS), un server applicazioni web che è certificato per Java Enterprise Edition 6.
- OFGW, il componente gateway OpenFrame che fornisce un listener 3270.
- OFManager, una soluzione che offre funzioni di funzionamento e alla gestione dell'OpenFrame nell'ambiente di web.

Componenti OpenFrame altro richiesti:

- OSI, la soluzione che sostituisce il controller di dominio IMS mainframe middleware.
- TJES, la soluzione che fornisce l'ambiente JES del mainframe.
- OFTSAM, la soluzione che consente di essere usati nel sistema open file SAM (V).
- OFHiDB, la soluzione che sostituisce il mainframe's DB IMS.
- OFPLI, un compilatore che interpreta il mainframe di PL / programmi.
- PROTRIEVE, una soluzione che esegue il linguaggio di mainframe Easytrieve di autorità di certificazione.
- OFMiner, una soluzione che consente di analizzare gli asset di mainframe e quindi ne esegue la migrazione ad Azure.

## <a name="architecture"></a>Architettura

Nella figura seguente viene fornita una panoramica dei componenti dell'architettura 7.0 OpenFrame installati in questa esercitazione:

![Componenti OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisiti di sistema di Azure

Nella tabella seguente elenca i requisiti per l'installazione in Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>DESCRIZIONE</th></tr>
</thead>
<tbody>
<tr><td>Distribuzioni di Linux supportate in Azure
</td>
<td>
Linux x86 (32 bit, 64 bit) 2.6<br/>
Red Hat 7.x<br/>
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
<tr><td>Software facoltativo per gli utenti di Windows
</td>
<td>PuTTY: Usato in questa guida per configurare le funzionalità di macchina virtuale<br/>
WinSCP: Un client SFTP più diffusi e un client FTP che è possibile usare<br/>
Eclipse per Windows: Una piattaforma di sviluppo supportata da TmaxSoft<br/>
(Microsoft Visual Studio non è supportato in questo momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Prerequisiti

Pianificare la spesa lievi assemblare tutti i software richiesti e completare tutti i processi manuali.

Prima di iniziare, eseguire le operazioni seguenti:

- Ottenere il supporto di installazione OpenFrame da TmaxSoft. Se sei un cliente esistente di TmaxSoft, contattare il rappresentante TmaxSoft per una copia con licenza. In caso contrario, richiedere una versione di valutazione [TmaxSoft](http://www.tmaxsoft.com/contact/).

- Richiedere la documentazione OpenFrame mediante l'invio di posta elettronica a <support@tmaxsoft.com>.

- Ottenere una sottoscrizione di Azure se non ne hai già uno. È anche possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- facoltativo. Configurare un tunnel VPN site-to-site o un jumpbox che limita l'accesso alla VM di Azure per gli utenti autorizzati all'interno dell'organizzazione. Questo passaggio non è obbligatorio, ma è una procedura consigliata.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurare una macchina virtuale in Azure per OpenFrame e Tibero

È possibile configurare l'ambiente OpenFrame usando vari modelli di distribuzione, ma la procedura seguente illustra come distribuire il server applicazioni OpenFrame e il database Tibero in una macchina virtuale. In ambienti più grandi e per i carichi di lavoro ridimensionabile, una procedura consigliata consiste nel distribuire il database separatamente nella relativa macchina virtuale per ottenere prestazioni migliori.

**Per creare una macchina virtuale**

1. Passare al portale di Azure all'indirizzo <http://portal.azure.com> ed eseguire l'accesso al proprio account.

2. Fare clic su **Virtual machines**.

    ![Elenco di risorse nel portale di Azure](media/vm-01.png)

3. Fare clic su **Aggiungi**.

    ![Aggiungere l'opzione nel portale di Azure](media/vm-02.png)

4. A destra della **sistemi operativi**, fare clic su **ulteriori**.

     ![Altre opzione nel portale di Azure](media/vm-03.png)

5. Fare clic su **basate su CentOS 7.3** seguire questa procedura dettagliata completa, oppure è possibile scegliere un'altra distribuzione Linux è supportato.

     ![Opzioni del sistema operativo nel portale di Azure](media/vm-04.png)

6. Nel **nozioni di base** le impostazioni, immettere **Name**, **nome utente**, **tipo di autenticazione**, **sottoscrizione** (Pagamento a consumo è lo stile AWS di pagamento), e **gruppo di risorse** (usarne uno esistente o creare un gruppo TmaxSoft).

7. Al termine (tra cui la coppia di chiavi pubblica/privata per **tipo di autenticazione**), fare clic su **Submit**.

> [!NOTE]
> Se si usa una chiave pubblica SSH per **tipo di autenticazione**, vedere i passaggi descritti nella sezione successiva per generare la coppia di chiavi pubblica/privata, quindi riprendere i passaggi indicati qui.

### <a name="generate-a-publicprivate-key-pair"></a>Generare una coppia di chiavi pubblica/privata

Se si usa un sistema operativo Windows, è necessario PuTTYgen per generare una coppia di chiavi pubblica/privata.

La chiave pubblica può essere condivisi liberamente, ma la chiave privata deve essere mantenuta segreta completamente e non deve mai essere condivisa con un'altra entità. Dopo la generazione delle chiavi, è necessario incollare il **chiave pubblica SSH** nella configurazione, in effetti, caricarlo in VM Linux. Viene archiviata autorizzati all'interno\_chiavi all'interno di \~directory /.ssh della home directory dell'account utente. Nella VM Linux è quindi in grado di riconoscere e convalidare la connessione dopo aver specificato l'oggetto associato **chiave privata SSH** nel client SSH (in questo caso, PuTTY).

Quando si forniscono nuovi utenti accedere alla macchina virtuale: 

- Ogni singolo nuovo genera le proprie chiavi pubblica/privata con PuTTYgen.
- Gli utenti archiviano le proprie chiavi private separatamente e inviare le informazioni sulla chiave pubbliche per l'amministratore della macchina virtuale.
- L'amministratore Incolla il contenuto della chiave pubblica per il \~/.ssh/authorized\_file delle chiavi.
- Il nuovo utente si connette tramite PuTTY.

**Per generare una coppia di chiavi pubblica/privata**

1.  Scaricare da PuTTYgen <https://www.putty.org/> e installarlo usando tutte le impostazioni predefinite.

2.  Per aprire PuTTYgen, individuare la directory di installazione PuTTY in c:\\Program Files\\PuTTY.

    ![Interfaccia puTTY](media/puttygen-01.png)

3.  Fare clic su **Genera**.

    ![Finestra di dialogo Generatore di chiavi puTTY](media/puttygen-02.png)

4.  Dopo la generazione, salvare la chiave privata sia la chiave pubblica. Incollare il contenuto della chiave pubblica nel **chiave pubblica SSH** sezione il **crea macchina virtuale \> nozioni di base** riquadro (illustrato nei passaggi 6 e 7 nella sezione precedente).

    ![Finestra di dialogo Generatore di chiavi puTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurare le funzionalità di macchina virtuale

1. Nel portale di Azure nel **Scegli una dimensione** blade, scegliere le impostazioni hardware di computer Linux da. Il *minimo* requisiti per l'installazione sia Tibero OpenFrame sono 2 CPU e 4 GB di RAM, come illustrato in questa installazione di esempio:

    ![Creare macchine virtuali - nozioni di base](media/create-vm-01.png)

2. Fare clic su **3 impostazioni** e usare le impostazioni predefinite per configurare le funzionalità facoltative.
3. Esaminare i dettagli di pagamento.

    ![Creare macchine virtuali - acquisto](media/create-vm-02.png)

4. Inviare le selezioni effettuate. Azure inizia a distribuire la macchina virtuale. Questo processo richiede in genere alcuni minuti.

5. Quando viene distribuita la macchina virtuale, viene visualizzato il dashboard corrispondente, che mostra tutte le impostazioni selezionate durante la configurazione. Annotare il **indirizzo IP pubblico**.

    ![tMax nel dashboard di Azure](media/create-vm-03.png)

6. Aprire PuTTY.

7. Per la **nome Host**, digitare il nome utente e l'indirizzo IP pubblico è copiato. Ad esempio, **nomeutente\@publicip**.

    ![Finestra di dialogo Configurazione puTTY](media/putty-01.png)

8. Nel **categoria** fare clic su **connessione \> SSH \> Auth**. Specificare il percorso per il **chiave privata** file.

    ![Finestra di dialogo Configurazione puTTY](media/putty-02.png)

9. Fare clic su **aperto** per avviare la finestra PuTTY. Se l'operazione riesce, si è connessi alla nuova macchina virtuale CentOS in esecuzione in Azure.

10. Per accedere come utente root, digitare **bash sudo**.

    ![Accesso dell'utente radice nella finestra di comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurare l'ambiente e pacchetti

Ora che la VM viene creata e si è connessi, è necessario eseguire alcuni passaggi di installazione e installare i pacchetti necessari di preinstallazione.

1. Il mapping del nome **ofdemo** all'indirizzo IP locale usando vi per modificare il file hosts (`vi /etc/hosts`). Supponendo che l'IP sia 192.168.96.148 ofdemo, questo viene prima della modifica:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Si tratta di dopo la modifica:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Creare utenti e gruppi:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Modificare la password per utente oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Aggiornare i parametri del kernel in /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Aggiornare i parametri del kernel in modo dinamico senza riavvio:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Ottenere i pacchetti richiesti: Verificare che il server sia connesso a Internet, scaricare i pacchetti seguenti e quindi installarli:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Dopo aver installato il pacchetto ncurses, creare i collegamenti simbolici seguenti:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Nel caso di installazione di RPM Java, seguire questa procedura:

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

## <a name="install-the-tibero-database"></a>Installare il database Tibero

Tibero fornisce le funzioni chiave diversi nell'ambiente OpenFrame in Azure:

- Tibero viene usato come archivio dati interno OpenFrame per varie funzioni di sistema.
- File VSAM, tra cui KSDS RRDS ed ESDS, usano il database Tibero internamente per l'archiviazione dei dati.
- Il repository dei dati TACF viene archiviato in Tibero.
- Le informazioni del catalogo OpenFrame viene archiviate in Tibero.
- Il database Tibero è utilizzabile come una sostituzione per IBM Db2 per archiviare i dati dell'applicazione.

**Per installare Tibero**

1. Verificare che sia presente il file di programma di installazione binari Tibero ed esaminare il numero di versione.
2. Copiare il software Tibero all'account utente Tibero (oframe). Ad esempio: 

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Aprire .bash\_profilo vi (`vi .bash_profile`) e incollare il codice seguente in esso:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Per eseguire il profilo di bash, al prompt dei comandi digitare:

    ```
    source .bash_profile
    ```

5. Generare il file di suggerimento (un file di configurazione per Tibero), quindi aprirlo vi. Ad esempio: 

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modificare \$TB\_HOME/client/config/tbdsn.tbr e inserire invece di 127.0.0.1 oflocalhost come illustrato:

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

8. Per riciclare Tibero, prima di tutto arrestarlo utilizzando il `tbdown` comando. Ad esempio: 

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Ora di avvio usando Tibero `tbboot`. Ad esempio: 

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Per creare uno spazio di tabella, accedere al database tramite SYS utente (sys/tmax), quindi creare lo spazio di tabella necessaria per il volume predefinita e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. A questo punto digitare i comandi SQL seguenti:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Avvio Tibero e verificare che i processi Tibero siano in esecuzione:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Output:

![Output Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Installare ODBC

Le applicazioni in OpenFrame comunicano con il database Tibero usando l'API ODBC fornito dal progetto open source unixODBC.

Per installare ODBC:

1. Verificare che sia presente il file di programma di installazione unixODBC-2.3.4.tar.gz oppure usare il `wget unixODBC-2.3.4.tar.gz` comando. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Decomprimere il file binario. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Passare alla directory unixODBC-2.3.4 e generare il file di progetto usando le informazioni di controllo della macchina. Ad esempio: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Per impostazione predefinita, unixODBC viene installato in /local /usr, quindi `--prefix` passa un valore per modificare il percorso. Analogamente, i file di configurazione vengono installati in /etc. per impostazione predefinita, pertanto `--sysconfdir` passa il valore del percorso desiderato.

4. Eseguire Makefile: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copiare il file eseguibile nella cartella del programma dopo la compilazione. Ad esempio: 

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Consente di modificare il profilo bash vi (`vi ~/.bash_profile`) e aggiungere quanto segue:

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

8. Creare un collegamento simbolico e convalidare la connessione al database Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Viene visualizzato l'output seguente:

![Output ODBC che mostra connessi a SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installare la Base OpenFrame

Il server applicazioni di Base viene installato prima i singoli servizi OpenFrame usati per gestire il sistema in Azure, compresa la transazione processi server di gestione.

**Per installare la OpenFrame Base**

1. Verificare che l'installazione Tibero ha avuto esito positivo, quindi verificare che il seguente OpenFrame\_Base7\_0\_Linux\_x86\_sono presenti file di programma di installazione a 64 bit bin e base.properties.

2. Aggiornare il profilo bash con le informazioni specifiche Tibero seguenti:

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
4. Assicurarsi che i processi Tibero siano in esecuzione. Ad esempio: 

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Assicurarsi di che avviare Tibero prima dell'installazione.

5. Generare una licenza all'indirizzo [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) e inserire la OpenFrame Base, Batch, TACF, Oschooser licenza nella cartella appropriata:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Scaricare i file binari e base.properties OpenFrame Base riportato di seguito:

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

7. Eseguire il programma di installazione utilizzando il file base.properties. Ad esempio: 

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Al termine dell'esercitazione, il messaggio completo di installazione viene visualizzata.

8. Verificare la struttura di directory OpenFrame Base utilizzando il `ls -ltr` comando. Ad esempio: 

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

9. Avviare OpenFrame Base riportato di seguito:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![output del comando tmboot](media/base-02.png)

10. Verificare che lo stato del processo è pronto con il comando tmadmin nel sistema internazionale di misura. RDY viene visualizzato nei **stato** colonna per ciascuno dei processi:

     ![output del comando tmadmin](media/base-03.png)

11. Arrestare OpenFrame Base riportato di seguito:

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

## <a name="install-openframe-batch"></a>Installare Batch OpenFrame

OpenFrame Batch è costituito da diversi componenti che simulano gli ambienti di batch mainframe e viene usato per eseguire processi batch in Azure.

**Installare Batch**

1. Verificare che l'installazione di base ha avuto esito positivo, quindi verificare che il OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_file di programma di installazione a 64 bit bin e file di configurazione batch.Properties sono presenti:

2. Al prompt dei comandi, digitare `vi batch.properties` per modificare il file batch.properties usando vi.

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

4. Per eseguire il programma di installazione di batch, al prompt dei comandi digitare:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Una volta completata l'installazione, avviare i pacchetti di OpenFrame installati digitando `tmboot` al prompt dei comandi.

    ![output tmboot](media/tmboot-01.png)

6. Tipo `tmadmin` al prompt dei comandi per controllare il processo OpenFrame.

    ![Schermata tMax Admin](media/tmadmin-01.png)

7. Eseguire i comandi seguenti:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Usare il `tmdown` comando per avviare e arrestare i Batch:

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

TACF Manager è un modulo di servizio OpenFrame che controlla l'accesso utente ai sistemi e le risorse mediante sicurezza RACF.

**Per installare TACF**

1. Verificare che il OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_sono presenti file di programma di installazione a 64 bit bin e tacf.properties.
2. Verificare che l'installazione di Batch ha avuto esito positivo, quindi usare vi per aprire il file tacf.properties (`vi tacf.properties`).
3. Modificare i parametri TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Al termine dell'installazione guidata TACF, si applicano le variabili di ambiente TACF. Al prompt dei comandi digitare:

     ```
     source \~/.bash\_profile
     ```

5. Eseguire il programma di installazione TACF. Al prompt dei comandi digitare:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     L'output simile al seguente:

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

6. Al prompt dei comandi, digitare `tmboot` riavviare OpenFrame. L'output simile al seguente:

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

7. Verificare che lo stato del processo sia pronto usando `tmadmin` nella `si` comando. Ad esempio: 

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Nel **stato** verrà visualizzata la colonna, RDY:

    ![RDY nella colonna status](media/tmboot-02.png)

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

9. Arrestare il server usando il `tmdown` comando. L'output simile al seguente:

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

## <a name="install-prosort"></a>Installazione ProSort

ProSort è un'utilità utilizzata nelle transazioni di batch per l'ordinamento dei dati.

**Per installare ProSort**

1. Assicurarsi che l'installazione di Batch è stata completata e quindi verificare che il **prosort prosort bin\_2sp3-linux64-2123-opt.tar.gz** file di programma di installazione è presente.

2. Eseguire il programma di installazione utilizzando il file delle proprietà. Al prompt dei comandi digitare:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Spostare la directory prosort la posizione iniziale. Al prompt dei comandi digitare:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Creare una sottodirectory di licenza e copiare il file di licenza non esiste. Ad esempio: 

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Aprire bash.profile vi (`vi .bash_profile`) e aggiornarlo come segue:

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

6. Per eseguire il profilo di bash, al prompt dei comandi, digitare: ` . .bash_profile`

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

9. Verificare l'installazione ProSort eseguendo il `prosort -h` comando. Ad esempio: 

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

1. Assicurarsi che l'installazione Online/Batch ha avuto esito positivo, quindi verificare che il OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_file di programma di installazione a 64 bit bin è presente.

2. Per eseguire il programma di installazione OFCOBOL, al prompt dei comandi, digitare:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Leggere il contratto di licenza e premere INVIO per continuare.

4. Accettare il contratto di licenza. Una volta completata l'installazione, viene visualizzato quanto segue:

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

5. Aprire il profilo bash vi (`vi .bash_profile`) e verificare che venga aggiornata con le variabili OFCOBOL.
6. Eseguire il profilo bash. Al prompt dei comandi digitare:

     ```
      source ~/.bash_profile
     ```

7. Copiare la licenza OFCOBOL installati nella cartella. Ad esempio: 
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Passare al file di configurazione tjclrun.conf OpenFrame e aprirlo in vi. Ad esempio: 
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Ecco la sezione SYSLIB prima della modifica:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Ecco la sezione SYSLIB dopo la modifica:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Esaminare il OpenFrame\_COBOL\_InstallLog.log file vi e verificare che non siano presenti errori. Ad esempio: 
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
10. Usare il `ofcob --version ` comando ed esaminare il numero di versione per verificare l'installazione. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Riavviare OpenFrame usando il `tmdown/tmboot` comando.

## <a name="install-ofasm"></a>Installare OFASM

OFASM è il compilatore OpenFrame che interpreta i programmi di assembler del mainframe.

**Per installare OFASM**

1. Assicurarsi che l'installazione Online/Batch ha avuto esito positivo, quindi verificare che il OpenFrame\_ASM3\_0\_Linux\_x86\_file di programma di installazione a 64 bit bin è presente.

2. Eseguire il programma di installazione. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leggere il contratto di licenza e premere INVIO per continuare.
4. Accettare il contratto di licenza.
5. Verificare che con le variabili OFASM viene aggiornato il profilo di bash. Ad esempio: 

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

6. Aprire il file di configurazione tjclrun.conf OpenFrame vi e modificarlo come indicato di seguito:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Di seguito è riportata la sezione [SYSLIB] *prima di* la modifica:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Di seguito è riportata la sezione [SYSLIB] *dopo* la modifica:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Aprire il OpenFrame\_ASM\_InstallLog.log file vi e verificare che non siano presenti errori. Ad esempio: 

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

8. Riavviare OpenFrame eseguendo uno dei seguenti comandi:

     ```
     tmdown / tmboot
     ```

     - o:

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installare Oschooser

Oschooser è l'ambiente OpenFrame simile a IBM CICS che supporta le transazioni OLTP ad alta velocità e altre funzioni di gestione.

**Per installare Oschooser**

1. Verificare che l'installazione di base ha avuto esito positivo, quindi verificare che il OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_sono file di programma di installazione a 64 bit bin e file di configurazione osc.properties presente.
2. Modificare i parametri seguenti nel file osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Eseguire il programma di installazione utilizzando il file delle proprietà, come illustrato:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Al termine, viene visualizzato il messaggio "Installazione completata".

4. Verificare che il profilo bash viene aggiornato con le variabili Oschooser.
5. Esaminare il OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log file. Dovrebbe essere visualizzata una schermata analoga alla seguente:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Consente di aprire il file di configurazione ofsys.seq vi. Ad esempio: 

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Nel \#BASE e \#BATCH sezioni, modificare i parametri, come illustrato.

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

9. Per avviare e arrestare Oschooser, inizializzare la memoria area condivisa CICS digitando `osctdlinit OSCOIVP1` al prompt dei comandi.

10. Eseguire `oscboot` per l'avvio Oschooser. L'output simile al seguente:

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

11. Per verificare che lo stato del processo è pronto, usare il `tmadmin` comando nel sistema internazionale di misura. Tutti i processi dovrebbero visualizzare RDY nel **stato** colonna.

    ![Processi visualizzando RDY](media/tmadmin-02.png)

12. Arrestare Oschooser utilizzando il `oscdown` comando.

## <a name="install-jeus"></a>Installare JEUS

JEUS (Java Enterprise utente Esplora) offre il livello di presentazione del server applicazioni web OpenFrame.

Prima di installare JEUS, installare il pacchetto Apache Ant, che offre le librerie e strumenti da riga di comando necessari per installare JEUS.

**Per installare Apache Ant**

1. Download Ant binario utilizzando il `wget` comando. Ad esempio: 

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Usare il `tar` utilità per estrarre il file binario e spostarlo in una posizione appropriata. Ad esempio: 

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Per motivi di efficienza, creare un collegamento simbolico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Aprire il profilo bash vi (`vi .bash_profile`) e lo aggiorna con le variabili seguenti:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Applicare la variabile di ambiente modificato. Ad esempio: 

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Per installare JEUS**

1. Espandere il programma di installazione utilizzando il `tar` utilità. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Creare un **jeus** cartella (`mkdir jeus7`) e decomprimere il file binario.
3. Modificare il **programma di installazione** directory (o usare il parametro JEUS per il proprio ambiente). Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Eseguire `ant clean-all` prima di eseguire la compilazione. L'output simile al seguente:

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

5.  Eseguire il backup del file di dominio-config-template.properties. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Aprire il file di dominio-config-template.properties vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Modifica `jeus.password=jeusadmin nodename=Tmaxsoft` a `jeus.password=tmax1234 nodename=ofdemo`

8. Eseguire il `ant install` comando per compilare JEUS.
9.  Aggiornare il .bash\_file del profilo con le variabili JEUS come illustrato:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Eseguire il profilo bash. Ad esempio: 

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Facoltativo*. Creare un alias per semplice arresto e avvio dei componenti JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Per verificare l'installazione, avviare il server di amministrazione di dominio, come illustrato:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verificare usando l'accesso al web usando la sintassi:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Ad esempio, <http://192.168.92.133:9736/webadmin/login.> viene visualizzata la schermata di accesso:
    
     ![Schermata di accesso del JEUS](media/jeus-01.png)

     > [!NOTE]
     > Se si riscontrano problemi con la protezione di porta, aprire la porta 9736 o disabilitare il firewall (`systemctl stop firewall`).

14. Per modificare il nome host per server1, fare clic su **bloccare & modificare**, quindi fare clic su **server1**. Nella finestra di Server, modificare il nome host come segue:

    1.  Change **Nodename** al **ofdemo**.
    2.  Fare clic su **OK** sul lato destro della finestra.
    3.  Fare clic su **applicare le modifiche** sul lato sinistro inferiore della finestra e per la descrizione, immettere *Hostname modifica*.

    ![Schermata del JEUS](media/jeus-02.png)

15. Verificare che la configurazione è riuscita nella schermata di conferma.

    ![schermata di Server jeus_domain](media/jeus-03.png)

16. Avviare il processo server gestito "server1" usando il comando seguente:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installare OFGW

OFGW è il gateway OpenFrame che supporta la comunicazione tra l'emulatore terminal 3270 e OSI base e gestisce le sessioni tra l'emulatore di terminale e OSI.

**Per installare OFGW**

1. Assicurarsi di aver JEUS è stato installato correttamente, quindi verificare che il OFGW7\_0\_1\_Generic.bin file di programma di installazione è presente.
2. Eseguire il programma di installazione. Ad esempio: 

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Usare i percorsi seguenti per i campi corrispondenti:
     -   JEUS Home directory
     -   Nome di dominio JEUS
     -   Nome del Server JEUS
     -   Driver Tibero
     -   ID del nodo tMax ofdemo

4. Accettare il resto delle impostazioni predefinite, quindi premere INVIO per uscire dal programma di installazione.

5. Verificare che l'URL per OFGW funzioni come previsto:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Viene visualizzata la schermata seguente:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installare OFManager

OFManager fornisce operazione e le funzioni di gestione per OpenFrame nell'ambiente di web.

**Per installare OFManager**

1. Verificare che il OFManager7\_Generic.bin file di programma di installazione è presente.
2. Eseguire il programma di installazione. Ad esempio: 

     ```
     OFManager7_Generic.bin
     ```

3.  Premere INVIO per continuare, quindi accettare il contratto di licenza.
4.  Scegliere la cartella di installazione.
5.  Accettare i valori predefiniti.
6.  Scegliere Tibero come il database.
7.  Premere INVIO per uscire dal programma di installazione.
8.  Verificare che l'URL per OFManager funzioni come previsto:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Viene visualizzata la schermata iniziale:

![Schermata di accesso tMax OpenFrame Manager](media/ofmanager-01.png)

Che viene completata l'installazione dei componenti OpenFrame.

## <a name="next-steps"></a>Passaggi successivi

Se si sta considerando una migrazione mainframe, ecosistema di partner a espansione è offrono supporto all'utente. Per indicazioni dettagliate sulla scelta di una soluzione dei partner, vedere [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Introduzione ad Azure](https://docs.microsoft.com/azure/)
-   [Documentazione di Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guida alla modalità Lift-and-Shift di Azure Data Center virtuale](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
