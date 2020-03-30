---
title: Installare TmaxSoft OpenFrame in macchine virtuali di AzureInstall TmaxSoft OpenFrame on Azure Virtual Machines
description: Riospita i carichi di lavoro mainframe IBM z/OS usando l'ambiente OpenFrame TmaxSoft nelle macchine virtuali di Azure.Rehost your IBM z/OS mainframe workloads using TmaxSoft OpenFrame environment on Azure Virtual Machines (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436055"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installare TmaxSoft OpenFrame in AzureInstall TmaxSoft OpenFrame on Azure

Informazioni su come configurare un ambiente OpenFrame in Azure adatto per carichi di lavoro di sviluppo, demo, test o produzione. Questa esercitazione illustra ogni passaggio.

OpenFrame includes multiple components that create the mainframe emulation environment on Azure. Ad esempio, i servizi online OpenFrame sostituiscono il middleware mainframe come IBM Customer Information Control System (CICS) e OpenFrame Batch, con il relativo componente TJES, sostituisce il Job Entry Subsystem (JES) del mainframe IBM.

OpenFrame funziona con qualsiasi database relazionale, inclusi Oracle Database, Microsoft SQL Server, IBM Db2 e MySQL. Questa installazione di OpenFrame utilizza il database relazionale TmaxSoft Tibero. Sia OpenFrame che Tibero funzionano su un sistema operativo Linux. Questo tutorial installa CentOS 7.3, anche se è possibile utilizzare altre distribuzioni Linux supportate. Il server applicazioni OpenFrame e il database Tibero vengono installati in una macchina virtuale (VM).

L'esercitazione illustra l'installazione dei componenti della suite OpenFrame. Alcuni devono essere installati separatamente.

Componenti OpenFrame principali:

- Pacchetti di installazione necessari.
- Database del Tbero.
- Open Database Connectivity (ODBC) viene utilizzato dalle applicazioni in OpenFrame per comunicare con il database Tibero.
- OpenFrame Base, il middleware che gestisce l'intero sistema.
- OpenFrame Batch, la soluzione che sostituisce i sistemi batch del mainframe.
- TACF, un modulo di servizio che controlla l'accesso degli utenti a sistemi e risorse.
- ProSort, uno strumento di ordinamento per le transazioni batch.
- OFCOBOL, un compilatore che interpreta i programmi COBOL del mainframe.
- OFASM, un compilatore che interpreta i programmi assembler del mainframe.
- OpenFrame Server Type C (OSC), la soluzione che sostituisce il middleware mainframe e IBM CICS.
- Java Enterprise User Solution (JEUS), un server applicazioni Web certificato per Java Enterprise Edition 6.
- OFGW, il componente gateway OpenFrame che fornisce un listener 3270.
- OFManager, una soluzione che fornisce le funzioni operative e di gestione di OpenFrame nell'ambiente Web.

Altri componenti OpenFrame necessari:

- OSI, la soluzione che sostituisce il middleware mainframe e IMS DC.
- TJES, la soluzione che fornisce l'ambiente JES del mainframe.
- OFTSAM, la soluzione che consente l'utilizzo di file SAM V (V)nel sistema aperto.
- OFHiDB, la soluzione che sostituisce il database IMS del mainframe.
- OFPLI, un compilatore che interpreta i programmi PL/I del mainframe.
- PROTRIEVE, una soluzione che esegue il linguaggio mainframe CA-Easytrieve.
- OFMiner, a solution that analyzes the mainframes assets and then migrates them to Azure.

## <a name="architecture"></a>Architecture

Nella figura seguente viene fornita una panoramica dei componenti dell'architettura OpenFrame 7.0 installati in questa esercitazione:The following figure provides an overview of the OpenFrame 7.0 architectural components installed in this tutorial:

![Componenti OpenFrame](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Requisiti di sistema di AzureAzure system requirements

The following table lists the requirements for the installation on Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Requisito</th><th>Descrizione</th></tr>
</thead>
<tbody>
<tr><td>Distribuzioni Linux supportate in AzureSupported Linux distributions on Azure
</td>
<td>
Linux x86 2.6 (32 bit, 64 bit)<br/>
Cappello Rosso 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Carote: 2 (minimo)<br/>
Memoria: 4 GB (minimo)<br/>
Spazio di swap: 1 GB (minimo)<br/>
Disco rigido: 100 GB (minimo)<br/>
</td>
</tr>
<tr><td>Software opzionale per utenti Windows
</td>
<td>PuTTY: utilizzato in questa guida per configurare le funzionalità della macchina virtualePuTTY: Used in this guide to configure VM features<br/>
WinSCP: un client SFTP e un client FTP più diffusi che è possibile utilizzare<br/>
Eclipse per Windows: una piattaforma di sviluppo supportata da TmaxSoft<br/>
(Microsoft Visual Studio non è supportato in questo momento)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Prerequisiti

Pianificare l'assemblaggio di tutti i software necessari e completare tutti i processi manuali.

Prima di iniziare, eseguire le operazioni seguenti:

- Ottenere il supporto di installazione OpenFrame da TmaxSoft.Get the OpenFrame installation media from TmaxSoft. Se sei un cliente TmaxSoft esistente, contatta il tuo rappresentante TmaxSoft per una copia con licenza. In caso contrario, richiedere una versione di prova da [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Richiedere la documentazione OpenFrame <support@tmaxsoft.com>inviando un messaggio di posta elettronica a .

- Ottenere una sottoscrizione di Azure se non ne è già inlicenza. Puoi anche creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Facoltativa. Configurare un tunnel VPN da sito a sito o un jumpbox che limita l'accesso alla macchina virtuale di Azure agli utenti consentiti nell'organizzazione. Questo passaggio non è obbligatorio, ma è una procedura consigliata.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Configurare una macchina virtuale in Azure per OpenFrame e TiberoSet up a VM on Azure for OpenFrame and Tibero

È possibile configurare l'ambiente OpenFrame utilizzando vari modelli di distribuzione, ma nella procedura seguente viene illustrato come distribuire il server applicazioni OpenFrame e il database Tibero in una macchina virtuale. In ambienti di grandi dimensioni e per carichi di lavoro di grandi dimensioni, è consigliabile distribuire il database separatamente nella propria macchina virtuale per migliorare le prestazioni.

**Per creare una macchina virtualeTo create a VM**

1. Passare al portale <https://portal.azure.com> di Azure e accedere all'account.

2. Fare clic su **Virtual machines**.

    ![Elenco delle risorse nel portale di AzureResource list in Azure portal](media/vm-01.png)

3. Fare clic su **Aggiungi**.

    ![Aggiungi opzione nel portale di AzureAdd option in Azure portal](media/vm-02.png)

4. A destra di **Sistemi operativi**fare clic su **Altro**.

     ![Altre opzioni nel portale di AzureMore option in Azure portal](media/vm-03.png)

5. Fare clic su **CentOS-based 7.3** per seguire esattamente questa procedura, oppure è possibile scegliere un'altra distribuzione Linux supportata.

     ![Opzioni del sistema operativo nel portale di AzureOperating System options in Azure portal](media/vm-04.png)

6. Nelle impostazioni **di Base** immettere **Nome**, **Nome utente**, Tipo **di autenticazione**, **Sottoscrizione** (con pagamento in base al consumo è lo stile di pagamento AWS) e Gruppo **di risorse** (utilizzare uno esistente o creare un gruppo TmaxSoft).

7. Al termine (inclusa la coppia di chiavi pubblica/privata per **Tipo di autenticazione**), fare clic su **Invia**.

> [!NOTE]
> Se si utilizza una chiave pubblica SSH per **Tipo di autenticazione**, vedere i passaggi nella sezione successiva per generare la coppia di chiavi pubblica/privata, quindi riprendere i passaggi qui.

### <a name="generate-a-publicprivate-key-pair"></a>Generare una coppia di chiavi pubblica/privata

Se si utilizza un sistema operativo Windows, è necessario PuTTYgen per generare una coppia di chiavi pubblica/privata.

La chiave pubblica può essere liberamente condivisa, ma la chiave privata deve essere mantenuta completamente segreta e non deve mai essere condivisa con un'altra parte. Dopo aver generato le chiavi, è necessario incollare la chiave pubblica SSH nella configurazione, in effetti, caricandola nella macchina virtuale Linux.After generating the keys, you must paste the **SSH public key** into the configuration, in effect, uploading it to the Linux VM. Viene memorizzato\_all'interno \~di chiavi autorizzate all'interno della directory /.ssh della home directory dell'account utente. La macchina virtuale Linux è quindi in grado di riconoscere e convalidare la connessione una volta fornita la **chiave privata SSH** associata nel client SSH (nel nostro caso, PuTTY).

Quando si concede a nuovi utenti l'accesso alla macchina virtuale:When giving new individuals access the VM: 

- Ogni nuovo individuo genera le proprie chiavi pubbliche/private utilizzando PuTTYgen.
- Gli utenti archiviano le proprie chiavi private separatamente e inviano le informazioni sulla chiave pubblica all'amministratore della macchina virtuale.
- L'amministratore incolla il contenuto della \~chiave pubblica nel\_file delle chiavi /.ssh/autorizzato.
- Il nuovo individuo si connette tramite PuTTY.

**Per generare una coppia di chiavi pubblica/privataTo generate a public/private key pair**

1.  Scaricare PuTTYgen <https://www.putty.org/> da e installarlo utilizzando tutte le impostazioni predefinite.

2.  Per aprire PuTTYgen, individuare la directory di\\installazione\\di PuTTY in C: Programmi PuTTY.

    ![Interfaccia PuTTY](media/puttygen-01.png)

3.  Fare clic su **Genera**.

    ![Finestra di dialogo Generatore di chiavi PuTTY](media/puttygen-02.png)

4.  Dopo la generazione, salvare sia la chiave pubblica che la chiave privata. Incollare il contenuto della chiave pubblica nella sezione relativa alla **chiave pubblica SSH** del riquadro Nozioni di base sulla creazione di **macchine \> virtuali** (illustrata nei passaggi 6 e 7 della sezione precedente).

    ![Finestra di dialogo Generatore di chiavi PuTTY](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Configurare le funzionalità della macchina virtualeConfigure VM features

1. Nel portale di Azure scegliere le impostazioni hardware del computer Linux desiderate nel pannello **Scegliere una dimensione.** I requisiti *minimi* per l'installazione di Tibero e OpenFrame sono 2 CPU e 4 GB di RAM, come illustrato in questa installazione di esempio:The minimum requirements for installing both Tibero and OpenFrame are 2 CPUs and 4 GB RAM as shown in this example installation:

    ![Creare una macchina virtuale - Nozioni di baseCreate virtual machine - Basics](media/create-vm-01.png)

2. Fare clic su **3 Impostazioni** e utilizzare le impostazioni predefinite per configurare le funzionalità facoltative.
3. Rivedi i dettagli del pagamento.

    ![Crea macchina virtuale - Acquisto](media/create-vm-02.png)

4. Inviare le selezioni. Azure inizia a distribuire la macchina virtuale. Questo processo richiede in genere alcuni minuti.

5. Quando la macchina virtuale viene distribuita, viene visualizzato il relativo dashboard, che mostra tutte le impostazioni selezionate durante la configurazione. Prendere nota **dell'indirizzo IP pubblico**.

    ![tmax nel dashboard di Azure](media/create-vm-03.png)

6. Aprire PuTTY.

7. In **Nome host**digitare il nome utente e l'indirizzo IP pubblico copiati. Ad esempio, **\@username publicip**.

    ![Finestra di dialogo Configurazione PuTTY](media/putty-01.png)

8. Nella casella **Categoria** fare clic su **Connection \> SSH \> Auth**. Specificare il percorso del file di **chiave privata.**

    ![Finestra di dialogo Configurazione PuTTY](media/putty-02.png)

9. Fare clic su **Apri** per aprire la finestra PuTTY. In caso di esito positivo, si è connessi alla nuova macchina virtuale CentOS in esecuzione in Azure.If successful, you are connected to your new CentOS VM running on Azure.

10. Per accedere come utente root, digitare **sudo bash**.

    ![Accesso utente root nella finestra di comando](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Configurare l'ambiente e i pacchetti

Ora che la macchina virtuale è stata creata e si è connessi, è necessario eseguire alcuni passaggi di installazione e installare i pacchetti di preinstallazione necessari.

1. Mappare il nome **didemo** all'indirizzo IP locale utilizzando`vi /etc/hosts`vi per modificare il file hosts ( ). Supponendo che il nostro IP è 192.168.96.148 didemo, questo è prima della modifica:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Questo è dopo la modifica:

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

4. Aggiornare i parametri del kernel in /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Aggiornare i parametri del kernel in modo dinamico senza riavviare:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Ottenere i pacchetti necessari: assicurarsi che il server sia connesso a Internet, scaricare i pacchetti seguenti e quindi installarli:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - Ncurses

          > [!NOTE]
          > Dopo aver installato il pacchetto ncurses, creare i seguenti collegamenti simbolici:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c
     - libaio-devel.x86\_64
     - Strace
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

## <a name="install-the-tibero-database"></a>Installare il database Tibero

Tibero provides the several key functions in the OpenFrame environment on Azure:

- Tibero viene utilizzato come archivio dati interno OpenFrame per varie funzioni di sistema.
- I file VSAM, inclusi KSDS, RRDS ed ESDS, utilizzano internamente il database Tibero per l'archiviazione dei dati.
- Il repository dei dati TACF è memorizzato in Tibero.
- Le informazioni del catalogo OpenFrame vengono memorizzate in Tibero.
- Il database Tibero può essere utilizzato in sostituzione di IBM Db2 per memorizzare i dati dell'applicazione.

**Per installare Tibero**

1. Verificare che il file del programma di installazione binario Tibero sia presente ed esaminare il numero di versione.
2. Copiare il software Tibero nell'account utente Tbero (oframe). Ad esempio:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Aprire il\_profilo .bash in vi (`vi .bash_profile`) e incollarvi quanto segue:

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

5. Generare il file di suggerimento (un file di configurazione per Tibero), quindi aprirlo in vi. Ad esempio:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Modificare \$\_TB HOME/client/config/tbdsn.tbr e inserire 127.0.0.1 anzichélocalhost come illustrato:

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

8. Per riciclare Tibero, prima `tbdown` spegnerlo utilizzando il comando. Ad esempio:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Ora avviare Tibero utilizzando `tbboot`. Ad esempio:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Per creare uno spazio tabelle, accedere al database utilizzando l'utente SYS (sys/tmax), quindi creare lo spazio tabelle necessario per il volume predefinito e TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. A questo punto digitare i seguenti comandi SQL:

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

![Uscita Tibero](media/tibero-01.png)

## <a name="install-odbc"></a>Installare ODBC

Le applicazioni in OpenFrame comunicano con il database Tibero utilizzando l'API ODBC fornita dal progetto unixODBC open-source.

Per installare ODBC:

1. Verificare che il file di installazione unixODBC-2.3.4.tar.gz sia presente o utilizzare il `wget unixODBC-2.3.4.tar.gz` comando . Ad esempio:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Decomprimere il file binario. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Passare alla directory unixODBC-2.3.4 e generare Makefile utilizzando le informazioni sulla macchina di controllo. Ad esempio:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Per impostazione predefinita, unixODBC viene installato `--prefix` in /usr /local, pertanto passa un valore per modificare il percorso. Analogamente, i file di configurazione vengono `--sysconfdir` installati in /etc per impostazione predefinita, pertanto passa il valore della posizione desiderata.

4. Esegui Makefile:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Copiare il file eseguibile nella directory del programma dopo la compilazione. Ad esempio:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Utilizzare vi per modificare`vi ~/.bash_profile`il profilo bash ( ) e aggiungere quanto segue:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Applicare il ODBC. Modificare i seguenti file di conseguenza. Ad esempio:

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

8. Creare un collegamento simbolico e convalidare la connessione al database Tibero:Create a symbolic link and validate the Tibero database connection:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Verrà visualizzato l'output seguente:

![ODBC output showing connected to SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installare OpenFrame Base

Il server applicazioni di base viene installato prima dei singoli servizi utilizzati da OpenFrame per gestire il sistema in Azure, inclusi i processi del server di gestione delle transazioni.

**Per installare OpenFrame Base**

1. Assicurarsi che l'installazione del Tibero sia\_stata\_eseguita correttamente, quindi verificare che siano presenti il seguente file di installazione OpenFrame Base7 0\_Linux\_x86.bin\_e il file di configurazione base.properties.

2. Aggiornare il profilo bash con le seguenti informazioni specifiche di Tibero:

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
4. Assicurarsi che i processi Del Tbero siano in esecuzione. Ad esempio:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Assicurarsi di avviare Tibero prima dell'installazione.

5. Generare la licenza [in](https://technet.tmaxsoft.com/en/front/main/main.do) technet.tmaxsoft.com e PUT le licenze OpenFrame Base, Batch, TACF, OSC nella cartella appropriata:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Scaricare i file OpenFrame Base binary e base.properties:

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

    Al termine, viene visualizzato il messaggio Installazione completata.

8. Verificare la struttura di `ls -ltr` directory base OpenFrame utilizzando il comando . Ad esempio:

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

9. Inizio base OpenFrame:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![output del comando tmboot](media/base-02.png)

10. Verificare che lo stato del processo sia pronto utilizzando il comando tmadmin in si. RDY viene visualizzato nella colonna dello **stato** per ciascuno dei processi:

     ![Output del comando tmadmin](media/base-03.png)

11. Arresta base OpenFrame:

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

## <a name="install-openframe-batch"></a>Installare OpenFrame Batch

OpenFrame Batch consists of several components that simulate mainframe batch environments and is used to run batch jobs on Azure.

**Per installare Batch**

1. Assicurarsi che l'installazione di base sia\_stata\_\_eseguita\_correttamente, quindi verificare che siano presenti il file del programma di installazione di OpenFrame Batch7 0 Fix2 MVS\_Linux\_x86\_64.bin e il file di configurazione batch.properties:

2. Al prompt dei `vi batch.properties` comandi, digitare per modificare il file batch.properties utilizzando vi.

3. Modificare i parametri come segue:

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

5. Al termine dell'installazione, avviare le suite `tmboot` OpenFrame installate digitando al prompt dei comandi.

    ![Uscita tmboot](media/tmboot-01.png)

6. Digitare `tmadmin` al prompt dei comandi per controllare il processo OpenFrame.

    ![Schermata Amministratore Tmax](media/tmadmin-01.png)

7. Eseguire i comandi seguenti:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Utilizzare `tmdown` il comando per avviare e arrestare Batch:

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

TACF Manager è un modulo di servizio OpenFrame che controlla l'accesso degli utenti ai sistemi e alle risorse tramite la sicurezza RACF.

**Per installare TACF**

1. Verificare che\_il file\_\_di\_installazione\_di\_OpenFrame Tacf7 0 Fix2 Linux x86 64.bin e il file di configurazione tacf.properties siano presenti.
2. Assicurarsi che l'installazione batch sia stata eseguita correttamente, quindi utilizzare vi per aprire il file tacf.properties ( ).`vi tacf.properties`
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

4. Dopo aver completato il programma di installazione TACF, applicare le variabili di ambiente TACF. Al prompt dei comandi digitare:

     ```
     source \~/.bash\_profile
     ```

5. Eseguire il programma di installazione TACF. Al prompt dei comandi digitare:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     L'output è simile al seguente:

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

6. Al prompt dei `tmboot` comandi digitare per riavviare OpenFrame. L'output è simile al seguente:

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

7. Verificare che lo stato `tmadmin` del `si` processo sia pronto utilizzando nel comando. Ad esempio:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     Nella colonna dello **stato** viene visualizzato RDY:

    ![RDY nella colonna dello stato](media/tmboot-02.png)

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

9. Arrestare il server `tmdown` utilizzando il comando. L'output è simile al seguente:

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

## <a name="install-prosort"></a>Installare ProSort

ProSort è un'utilità utilizzata nelle transazioni batch per l'ordinamento dei dati.

**Per installare ProSort**

1. Assicurarsi che l'installazione batch sia stata eseguita correttamente, quindi verificare che il file di installazione **\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz** sia presente.

2. Eseguire il programma di installazione utilizzando il file delle proprietà. Al prompt dei comandi digitare:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Spostare la directory prosort nella posizione iniziale. Al prompt dei comandi digitare:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Creare una sottodirectory di licenza e copiarvi il file di licenza. Ad esempio:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Aprire bash.profile in`vi .bash_profile`vi ( ) e aggiornarlo come segue:

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

6. Per eseguire il profilo bash, al prompt dei comandi digitare:`. .bash_profile`

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

9. Verificare l'installazione di `prosort -h` ProSort eseguendo il comando . Ad esempio:

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

1. Assicurarsi che l'installazione batch/online sia stata\_eseguita\_correttamente, quindi verificare che sia presente il file del programma di installazione OpenFrame COBOL3\_0 40\_Linux\_x86\_64.bin.

2. Per eseguire il programma di installazione OFCOBOL, al prompt dei comandi digitare:

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

5. Aprire il profilo bash`vi .bash_profile`in vi ( ) e verificare che sia aggiornato con le variabili OFCOBOL.
6. Eseguire il profilo bash. Al prompt dei comandi digitare:

     ```
      source ~/.bash_profile
     ```

7. Copiare la licenza OFCOBOL nella cartella installata. Ad esempio:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Passare al file di configurazione OpenFrame tjclrun.conf e aprirlo in vi. Ad esempio:
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
9. Esaminare il\_file\_OpenFrame COBOL InstallLog.log in vi e verificare che non siano presenti errori. Ad esempio:
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
10. Utilizzare `ofcob --version` il comando ed esaminare il numero di versione per verificare l'installazione. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Riavviare OpenFrame `tmdown/tmboot` utilizzando il comando .

## <a name="install-ofasm"></a>Installazione di OFASM

OFASM è il compilatore OpenFrame che interpreta i programmi assembler del mainframe.

**Per installare OFASM**

1. Assicurarsi che l'installazione batch/online sia stata\_eseguita\_correttamente, quindi verificare che il file del programma di installazione OpenFrame ASM3 0\_Linux\_x86\_64.bin sia presente.

2. Eseguire il programma di installazione. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Leggere il contratto di licenza e premere INVIO per continuare.
4. Accettare il contratto di licenza.
5. Verificare che il profilo bash sia aggiornato con le variabili OFASM. Ad esempio:

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

6. Aprire il file di configurazione OpenFrame tjclrun.conf in vi e modificarlo come segue:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Di seguito è riportata la sezione [SYSLIB] *prima* della modifica:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Di seguito è riportata la sezione [SYSLIB] *dopo* la modifica:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Aprire il\_file\_OpenFrame ASM InstallLog.log in vi e verificare che non siano presenti errori. Ad esempio:

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

     -oppure-

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installare OSC

OSC è l'ambiente OpenFrame simile a IBM CICS che supporta le transazioni OLTP ad alta velocità e altre funzioni di gestione.

**Per installare OSC**

1. Assicurarsi che l'installazione di base sia\_stata eseguita correttamente, quindi verificare che siano presenti il file di installazione di OpenFrame\_OSC7\_0 Fix2\_Linux\_x86\_64.bin e il file di configurazione osc.properties.
2. Modificare i seguenti parametri nel file osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Eseguire il programma di installazione utilizzando il file delle proprietà come illustrato:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Al termine, viene visualizzato il messaggio "Installazione completata".

4. Verificare che il profilo bash sia aggiornato con le variabili OSC.
5. Esaminare il\_file InstallLog.log di OpenFrame OSC7\_0\_Fix2.\_ L'aspetto dell'anteprima dovrebbe essere simile al seguente:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Utilizzare vi per aprire il file di configurazione ofsys.seq. Ad esempio:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Nelle \#sezioni \#BASE e BATCH, modificare i parametri come mostrato.

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

9. Per avviare e arrestare OSC, inizializzare la `osctdlinit OSCOIVP1` memoria condivisa dell'area CICS digitando al prompt dei comandi.

10. Eseguire `oscboot` per avviare OSC. L'output è simile al seguente:

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

11. Per verificare che lo stato `tmadmin` del processo sia pronto, utilizzare il comando in si. Tutti i processi devono visualizzare RDY nella colonna dello **stato.**

    ![Processi che visualizzano RDY](media/tmadmin-02.png)

12. Arrestare OSC `oscdown` utilizzando il comando.

## <a name="install-jeus"></a>Installare JEUS

JEUS (Java Enterprise User Solution) fornisce il livello di presentazione del server applicazioni Web OpenFrame.

Prima di installare JEUS, installare il pacchetto Apache Ant, che fornisce le librerie e gli strumenti della riga di comando necessari per installare JEUS.

**Per installare Apache Ant**

1. Scaricare Ant binary `wget` utilizzando il comando. Ad esempio:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Utilizzare `tar` l'utilità per estrarre il file binario e spostarlo in una posizione appropriata. Ad esempio:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Per migliorare l'efficienza, creare un collegamento simbolico:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Aprire il profilo bash`vi .bash_profile`in vi ( )e aggiornarlo con le seguenti variabili:

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

1. Espandere il programma `tar` di installazione utilizzando l'utilità. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Creare una cartella`mkdir jeus7` **jeus** ( ) e decomprimere il file binario.
3. Passare alla directory di **installazione** (o utilizzare il parametro JEUS per il proprio ambiente). Ad esempio:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Eseguire `ant clean-all` prima di eseguire la compilazione. L'output è simile al seguente:

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

5.  Eseguire una copia di backup del file domain-config-template.properties. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Aprire il file domain-config-template.properties in vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Modificare `jeus.password=jeusadmin nodename=Tmaxsoft` in `jeus.password=tmax1234 nodename=ofdemo`

8. Eseguire `ant install` il comando per compilare JEUS.
9.  Aggiornare il\_file del profilo .bash con le variabili JEUS come mostrato:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Eseguire il profilo bash. Ad esempio:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Facoltativo*. Creare un alias per semplificare l'arresto e l'avvio dei componenti JEUS:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Per verificare l'installazione, avviare il server di amministrazione del dominio come illustrato di seguito:To verify the installation, start the domain admin server as shown:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verificare tramite accesso Web utilizzando la sintassi:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Ad esempio, <http://192.168.92.133:9736/webadmin/login.> viene visualizzata la schermata di accesso:
    
     ![Schermata di accesso JEUS WebAdmin](media/jeus-01.png)

     > [!NOTE]
     > Se si verificano problemi con la sicurezza della porta,`systemctl stop firewall`aprire la porta 9736 o disattivare il firewall ( ).

14. Per modificare il nome host per server1, fare clic su **Blocca & Modifica**, quindi su **server1**. Nella finestra Server, modificare il nome host come segue:

    1.  Impostare **NomeNodo su** **ofdemo**.
    2.  Fare clic **su OK** sul lato destro della finestra.
    3.  Fare clic su **Applica modifiche** nella parte inferiore sinistra della finestra e, per la descrizione, immettere *Hostname change*.

    ![Schermata WebAdmin JEUS](media/jeus-02.png)

15. Verificare che la configurazione sia stata eseguita correttamente nella schermata di conferma.

    ![Schermata jeus_domain Server](media/jeus-03.png)

16. Avviare il processo del server gestito "server1" utilizzando il comando seguente:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installazione di OFGW

OFGW è il gateway OpenFrame che supporta la comunicazione tra l'emulatore di terminale 3270 e la base OSI e gestisce le sessioni tra l'emulatore di terminale e OSI.

**Per installare OFGW**

1. Assicurarsi che JEUS sia stato installato correttamente,\_\_quindi\_verificare che il file di installazione OFGW7 0 1 Generic.bin sia presente.
2. Eseguire il programma di installazione. Ad esempio:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Utilizzare le seguenti posizioni per i prompt corrispondenti:
     -   Home directory JEUS
     -   Nome di dominio JEUS
     -   Nome server JEUS
     -   Autista del Tbero
     -   ID nodo Tmax ofdemo

4. Accettare il resto delle impostazioni predefinite, quindi premere INVIO per uscire dal programma di installazione.

5. Verificare che l'URL per OFGW funzioni come previsto:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Verrà visualizzata la seguente schermata:

    ![Finestra Web OpenFrame](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installare OFManager

OFManager fornisce funzioni operative e di gestione per OpenFrame nell'ambiente Web.

**Per installare OFManager**

1. Verificare che il\_file del programma di installazione OFManager7 Generic.bin sia presente.
2. Eseguire il programma di installazione. Ad esempio:

     ```
     OFManager7_Generic.bin
     ```

3.  Premere INVIO per continuare, quindi accettare il contratto di licenza.
4.  Scegliere la cartella di installazione.
5.  Accettare i valori predefiniti.
6.  Scegliere Tbero come database.
7.  Premere Invio per uscire dal programma di installazione.
8.  Verificare che l'URL per OFManager funzioni come previsto:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Viene visualizzata la schermata iniziale:

![Schermata di accesso di Tmax OpenFrame Manager](media/ofmanager-01.png)

Che completa l'installazione dei componenti OpenFrame.

## <a name="next-steps"></a>Passaggi successivi

Se stai considerando una migrazione mainframe, il nostro ecosistema di partner in espansione è disponibile per aiutarti. Per indicazioni dettagliate sulla scelta di una soluzione dei partner, vedere [Platform Modernization Alliance](https://datamigration.microsoft.com/).

-   [Introduzione ad Azure](https://docs.microsoft.com/azure/)
-   [Documentazione di Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Guida di sollevamento e spostamento del centro dati virtuale di AzureAzure Virtual Data Center Lift-and-Shift Guide](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
