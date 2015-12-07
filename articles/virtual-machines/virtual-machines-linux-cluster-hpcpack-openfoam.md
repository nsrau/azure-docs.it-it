<properties
 pageTitle="Eseguire OpenFOAM con HPC Pack in macchine virtuali Linux | Microsoft Azure"
 description="Informazioni su come distribuire un cluster Microsoft HPC Pack in Azure ed eseguire un processo OpenFOAM in più nodi di calcolo Linux su una rete RDMA."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="11/22/2015"
 ms.author="danlep"/>

# Eseguire OpenFoam con Microsoft HPC Pack in un cluster Linux RDMA in Azure

Questo articolo descrive come distribuire un cluster Microsoft HPC Pack in Azure ed eseguire un processo [OpenFoam](http://openfoam.com/) con Intel MPI su più nodi di calcolo Linux che si connettono attraverso la rete con tecnologia Accesso diretto a memoria remota (RDMA) di Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

OpenFOAM (che sta per Open Field Operation and Manipulation) è un pacchetto software di fluidodinamica computazionale (CFD) open source disponibile gratuitamente ampiamente usato in ambito ingegneristico e scientifico, in organizzazioni di tipo commerciale e accademico. Include strumenti per la generazione di mesh, ad esempio snappyHexMesh, uno strumento di generazione mesh parallelizzato per geometrie CAD complesse, per la pre e la post-elaborazione. Quasi tutti i processi vengono eseguiti in parallelo, per consentire agli utenti di sfruttare appieno l'hardware IT a disposizione.

Microsoft HPC Pack fornisce le funzionalità necessarie per eseguire svariate applicazioni HPC e parallele su larga scala, incluse le applicazioni MPI, in cluster di macchine virtuali di Microsoft Azure. A partire da Microsoft HPC Pack 2012 R2 Update 2, HPC Pack supporta anche l'esecuzione di applicazioni HPC Linux su macchine virtuali del nodo di calcolo Linux distribuite in un cluster HPC Pack. Per informazioni introduttive sull'uso dei nodi di calcolo Linux con HPC Pack, vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md).

>[AZURE.NOTE]Questo articolo presuppone che l'utente abbia familiarità con l'amministrazione del sistema Linux e con l'esecuzione di carichi di lavoro MPI in cluster HPC Linux.

## Prerequisiti

*   **Cluster HPC Pack con nodi di calcolo Linux**: vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md) per informazioni sui prerequisiti e sui passaggi da eseguire per distribuire un cluster HPC Pack con nodi di calcolo Linux in Azure usando uno script di Azure PowerShell e immagini di HPC Pack disponibili in Azure Marketplace. Per considerazioni aggiuntive sull'utilizzo di istanze A8 a elevato utilizzo di calcolo per accedere alla rete RDMA di Azure, vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](virtual-machines-a8-a9-a10-a11-specs.md).

    Di seguito è riportato un file di configurazione XML di esempio da usare con lo script per distribuire un cluster HPC Pack basato su Azure, costituito da un nodo head Windows Server 2012 R2 di dimensioni A8 e 2 nodi di calcolo SUSE Linux Enterprise Server 12 di dimensioni A8. Sostituire i valori dell'esempio con i valori appropriati per la sottoscrizione e i nomi dei servizi.

    >[AZURE.NOTE]Attualmente, la tecnologia di rete Linux RDMA in Azure è supportata solo nelle macchine virtuali create dall'immagine di SUSE Linux Enterprise Server 12 abilitata per RDMA disponibile in Azure Marketplace (b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-sles-12-hpc-v20150708).

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>allvhdsje</StorageAccount>
      </Subscription>
      <Location>Japan East</Location>  
      <VNet>
        <VNetName>suse12rdmavnet</VNetName>
        <SubnetName>SUSE12RDMACluster</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>SUSE12RDMA-HN</VMName>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <NodeCount>2</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```

    **Altre informazioni importanti**

    *   Distribuire tutti i nodi di calcolo Linux all'interno di un servizio cloud per usare la connessione di rete RDMA tra i nodi.

    *   Dopo aver distribuito i nodi Linux, se è necessaria una connessione tramite SSH per eseguire eventuali attività amministrative aggiuntive, accedere al portale di Azure per trovare i dettagli relativi alla connessione SSH per ogni macchina virtuale Linux.
        
*   **Intel MPI**: per eseguire OpenFOAM su nodi di calcolo Linux in Azure, è necessario scaricare il runtime di Intel MPI Library 5 dal [sito Intel.com](https://software.intel.com/it-IT/intel-mpi-library/). In un passaggio successivo si procederà all'installazione di Intel MPI nei nodi di calcolo Linux. Come preparazione, dopo aver eseguito la registrazione a Intel, fare clic sul collegamento contenuto nel messaggio di posta elettronica di conferma per accedere alla relativa pagina Web e copiare il collegamento di download del file con estensione tgz per la versione appropriata di Intel MPI. Questo articolo si riferisce a Intel MPI versione 5.0.3.048.

*   **OpenFOAM Source Pack**: scaricare il software OpenFOAM Source Pack per Linux dal [sito OpenFOAM Foundation](http://www.openfoam.org/download/source.php). In questo articolo viene usato Source Pack versione 2.3.1, disponibile per il download come OpenFOAM 2.3.1.tgz. Seguire le istruzioni riportate più avanti in questo articolo per decomprimere e compilare OpenFOAM nei nodi di calcolo Linux.

*   **EnSight** (facoltativo): per visualizzare i risultati della simulazione OpenFOAM, scaricare e installare la versione di Windows del programma di analisi e visualizzazione [EnSight](https://www.ceisoftware.com/download/) nel nodo head del cluster HPC Pack. Per informazioni sul download e la gestione delle licenze, vedere il sito EnSight.


## Configurare il trust reciproco tra i nodi di calcolo

L'esecuzione di un processo su più nodi Linux richiede una relazione di trust tra i nodi (tramite **rsh** o **ssh**). Quando si crea il cluster HPC Pack con lo script di distribuzione IaaS di Microsoft HPC Pack, lo script configura automaticamente un trust reciproco permanente per l'account amministratore specificato. Per gli utenti non amministratori creati nel dominio del cluster è necessario configurare una relazione di trust reciproco temporanea tra i nodi in caso di allocazione di un processo e quindi eliminare la relazione dopo il completamento del processo. Per eseguire questa operazione per ogni utente, fornire una coppia di chiavi RSA al cluster usato da HPC Pack per stabilire la relazione di trust.

### Generare una coppia di chiavi RSA

Per generare con facilità una coppia di chiavi RSA, che contiene una chiave pubblica e una chiave privata, eseguire il comando **ssh-keygen** di Linux.

1.	Accedere a un computer Linux.

2.	Eseguire il comando indicato di seguito.

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]Premere **INVIO** per usare le impostazioni predefinite fino a quando il comando non viene completato. Non immettere una passphrase. Quando viene richiesta una password, è sufficiente premere **INVIO**.

    ![Generare una coppia di chiavi RSA][keygen]

3.	Passare alla directory ~/.ssh. La chiave privata viene archiviata in id\_rsa e la chiave pubblica in id\_rsa.pub.

    ![Chiavi pubbliche e private][keys]

### Aggiungere la coppia di chiavi al cluster HPC Pack
1.	Stabilire una connessione Desktop remoto al nodo head con l'account amministratore di HPC Pack, ovvero l'account amministratore configurato durante l'esecuzione dello script di distribuzione.

2. Usare le procedure standard di Windows Server per creare un account utente di dominio nel dominio di Active Directory del cluster. Ad esempio, usare lo strumento Utenti e computer di Active Directory sul nodo head. Gli esempi in questo articolo presuppongono che venga creato un utente di dominio denominato hpclab\\hpcuser.

3.	Creare un file con nome C:\\cred.xml e copiarvi i dati della chiave RSA. Un esempio di questo file è disponibile nell'Appendice alla fine dell'articolo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.	Aprire un prompt dei comandi e immettere il comando seguente per impostare i dati delle credenziali per l'account hpclab\\hpcuser. Usare il parametro **extendeddata** per passare il nome del file C:\\cred.xml creato per i dati della chiave.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Questo comando viene completato correttamente senza output. Dopo avere configurato le credenziali per gli account utente necessari per l'esecuzione dei progetti, archiviare il file cred.xml in un percorso sicuro oppure eliminarlo.

5.	Se la coppia di chiavi RSA è stata generata in uno dei nodi Linux, ricordare di eliminare le chiavi dopo averle usate. HPC Pack non configura il trust reciproco se rileva un file id\_rsa o id\_rsa.pub esistente.

>[AZURE.IMPORTANT]Non è consigliabile eseguire un processo di Linux come amministratore del cluster in un cluster condiviso, perché un processo inviato da un amministratore viene eseguito con l'account radice nei nodi Linux. Un processo inviato da un utente non amministratore viene eseguito con un account utente Linux locale, con lo stesso nome dell'utente del processo, e HPC Pack configura il trust reciproco per questo utente Linux in tutti i nodi allocati al processo. È possibile configurare manualmente l'utente Linux nei nodi Linux prima di eseguire il processo. In alternativa, HPC Pack crea automaticamente l'utente quando viene inviato il processo. Se HPC Pack crea l'utente, HPC Pack lo eliminerà dopo il completamento del processo. Le chiavi vengono rimosse dopo il completamento del processo sui nodi, per ridurre le minacce alla sicurezza.

## Configurare una condivisione di file per i nodi Linux

Configurare ora una condivisione SMB standard per una cartella nel nodo head, quindi montare la cartella condivisa in tutti i nodi Linux per consentire ai nodi Linux di accedere ai file dell'applicazione con un percorso comune. In alternativa, è possibile usare un'altra opzione di condivisione, ad esempio una condivisione File di Azure, consigliata per molti scenari, oppure una condivisione NFS. Per informazioni sulle opzioni di condivisione file e sulle procedure dettagliate, vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md).

1.	Creare una cartella sul nodo head e condividerla con tutti gli utenti, configurando privilegi di lettura/scrittura. Ad esempio, condividere C:\\OpenFOAM nel nodo head come \\\SUSE12RDMA-HN\\OpenFOAM. In questo caso, *SUSE12RDMA-HN* è il nome host del nodo head.

2.	Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per montare la cartella condivisa.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>’`,dir_mode=0777`,file_mode=0777
    ```

Il primo comando crea una cartella denominata /openfoam in tutti i nodi del gruppo LinuxNodes. Il secondo comando monta la cartella condivisa //SUSE12RDMA-HN/OpenFOAM nei nodi Linux con i bit dir\_mode e file\_mode impostati su 777. I valori di *username* e *password* nel comando devono corrispondere alle credenziali di un utente nel nodo head.

>[AZURE.NOTE]Il simbolo "`" nel secondo comando è un simbolo di escape per PowerShell. "`," significa che "," (virgola) è una parte del comando.

## Installare MPI e OpenFOAM

Per eseguire OpenFOAM come un processo MPI nella rete RDMA, è necessario compilare OpenFOAM con le librerie Intel MPI.

Per prima cosa, sarà necessario eseguire diversi comandi **clusrun** per installare le librerie Intel MPI e OpenFOAM in tutti i nodi Linux. Usare la condivisione del nodo head configurata in precedenza per condividere i file di installazione tra i nodi Linux.

>[AZURE.IMPORTANT]Queste procedure di installazione e di compilazione sono esempi e richiedono una conoscenza di base dell'amministrazione del sistema Linux, in particolare per garantire che le librerie e compilatori dipendenti siano installati correttamente. Potrebbe essere necessario modificare alcune variabili di ambiente o altre impostazioni necessarie per le versioni di Intel MPI e OpenFOAM in uso. Per informazioni dettagliate vedere gli articoli relativi alla [guida all'installazione di Intel MPI Library per Linux](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html) e all'[installazione di OpenFOAM Source Pack](http://www.openfoam.org/download/source.php).


### Installare Intel MPI

Salvare il pacchetto di installazione scaricato per Intel MPI (in questo esempio l\_mpi\_p\_5.0.3.048.tgz) in C:\\OpenFoam nel nodo head in modo che i nodi Linux possano accedere a questo file da /openfoam. Eseguire quindi il comando **clusrun** per installare Intel MPI Library in tutti i nodi Linux.

1.  I comandi seguenti consentono di copiare il pacchetto di installazione e di estrarlo nella cartella /opt/intel in ogni nodo.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Per eseguire un'installazione di Intel MPI Library invisibile all'utente, usare un file silent.cfg. Un esempio di questo file è disponibile nell'Appendice alla fine dell'articolo. Posizionare questo file nella cartella condivisa /openfoam. Per informazioni dettagliate sul file silent.cfg, vedere l'articolo relativo alla [guida all'installazione di Intel MPI Library per Linux - Installazione invisibile all'utente](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html#silentinstall).

    >[AZURE.TIP]Assicurarsi di salvare il file silent.cfg come file di testo con terminazioni di riga Linux (solo LF, non CR LF), in modo da assicurarne l'esecuzione corretta nei nodi Linux.

3.  Installare Intel MPI Library in modalità invisibile all'utente.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### Configurare MPI

Ai fini del test, aggiungere le righe seguenti al file /etc/security/limits.conf nei nodi Linux:

```
*               hard    memlock         unlimited
*               soft    memlock         unlimited
```

È possibile eseguire questa operazione creando un file limits.conf in C:\\OpenFoam (salvare il file di testo con terminazioni di riga Linux) ed eseguire il comando seguente per copiarlo nei nodi Linux:

```
clusrun /nodegroup:LinuxNodes cp /openfoam/limits.conf /etc/security
```

Dopo aver aggiornato il file limits.conf, riavviare i nodi Linux. Dopo il riavvio, assicurarsi che la cartella condivisa sia montata come /openfoam.

### Compilare e installare OpenFOAM

Salvare il pacchetto di installazione scaricato per OpenFOAM Source Pack (in questo esempio OpenFOAM-2.3.1.tgz) in C:\\OpenFoam nel nodo head in modo che i nodi Linux possano accedere a questo file da /openfoam. Eseguire quindi il comando **clusrun** per compilare OpenFOAM in tutti i nodi Linux.


1.  Creare una cartella /opt/OpenFOAM in ogni nodo Linux, copiare il pacchetto di origine a questa cartella e decomprimerlo in questa posizione.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Per compilare OpenFOAM con Intel MPI Library, è prima necessario configurare alcune variabili di ambiente per Intel MPI e OpenFOAM. A tale scopo, usare uno script bash denominato settings.sh. Un esempio di questo file è disponibile nell'Appendice alla fine dell'articolo. Copiare questo file (salvato con terminazioni riga Linux) nella cartella condivisa /openfoam. Questo file contiene anche le impostazioni per i runtime MPI e OpenFOAM che verranno usati più avanti per eseguire un processo OpenFOAM.

3. Installare i pacchetti dipendenti necessari per compilare OpenFOAM. A seconda della distribuzione Linux corrente, potrebbe essere prima necessario aggiungere diversi repository. I repository e i pacchetti sono elencati nella sezione Appendice alla fine di questo articolo. È consigliabile stabilire una connessione a ogni nodo Linux tramite SSH per eseguire i comandi e verificare che funzionino correttamente.

4.  Eseguire il comando seguente per compilare OpenFOAM. Il processo di compilazione richiederà diverso tempo e genererà una grande quantità di informazioni di log nell'output standard, quindi usare l'opzione **/ interleaved** per visualizzare l'output con interfoliazione.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```

>[AZURE.NOTE]Il simbolo "`" nel secondo comando è un simbolo di escape per PowerShell. "`&" indica che "&" è parte del comando.

## Preparare l'esecuzione di un processo OpenFOAM

A questo punto, verrà eseguito un processo MPI denominato sloshingTank3D, uno degli esempi di OpenFOAM, in 2 nodi Linux. In questo esempio, /opt/openfoam231 è il percorso di installazione di OpenFOAM nei nodi Linux.

### Configurare l'ambiente di runtime

Eseguire il comando seguente in una finestra di Windows PowerShell nel nodo head per configurare gli ambienti di runtime per MPI e OpenFOAM in tutti i nodi Linux.

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### Preparare i dati di esempio

Usare la condivisione del nodo head configurata in precedenza per condividere i file tra i nodi Linux (montata come /openfoam).

1.  Stabilire una connessione SSH a uno dei nodi di calcolo Linux.

2.  Eseguire il comando seguente per configurare l'ambiente di runtime OpenFOAM, se non è già stato fatto.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copiare l'esempio sloshingTank3D nella cartella condivisa e passare a questa cartella.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Quando si usano i parametri predefiniti di questo esempio, l'esecuzione può richiedere tempi molto prolungati, è quindi consigliabile modificare alcuni parametri per accelerare i tempi di esecuzione. Una modo semplice prevede la modifica delle variabili delle fasi temporali deltaT e writeInterval nel file system/controlDict in cui vengono archiviati tutti i dati di input relativi al controllo di tempo, lettura e scrittura dei dati della soluzione. È ad esempio possibile modificare il valore di deltaT da 0,05 a 0,5 e il valore di writeInterval da 0,05 a 0,5.

    ![Modificare le variabili delle fasi][step_variables]

5.  Specificare i valori desiderati per le variabili nel file system/decomposeParDict. Questo esempio usa 2 nodi Linux con 8 core ciascuno, quindi impostare numberOfSubdomains su 16 e il numero di hierarchicalCoeffs su (1 1 16) per specificare di eseguire OpenFOAM in parallelo con 16 processi. Per altre informazioni su come eseguire OpenFOAM in parallelo, vedere la sezione [3\.4 del manuale dell'utente di OpenFOAM relativa all'esecuzione di applicazioni in parallelo](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Scomporre i processi][decompose]

6.  Eseguire i comandi seguenti dalla directory sloshingTank3D per preparare i dati di esempio.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  Nel nodo head i file dei dati di esempio verranno copiati in C:\\OpenFoam\\sloshingTank3D. C:\\OpenFoam è la cartella condivisa nel nodo head.

    ![File di dati nel nodo head][data_files]

### File host per mpirun

In questo passaggio verrà creato un file host (un elenco di nodi di calcolo) che verrà usato dal comando **mpirun**.

1.	In uno dei nodi Linux creare un nuovo file denominato hostfile in /openfoam, in modo che questo file sia disponibile nel percorso /openfoam/hostfile in tutti i nodi Linux.

2.	Scrivere i nomi dei nodi Linux in questo file. In questo esempio, il file avrà un aspetto simile al seguente:
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]È anche possibile creare questo file in C:\\OpenFoam\\hostfile nel nodo head. In questo caso, sarà necessario salvare lo script come file di testo con terminazioni di riga Linux (solo LF, non CR LF), in modo da assicurarne l'esecuzione corretta nei nodi Linux.

    **Wrapper di script bash**

    Se sono presenti molti nodi Linux e il processo verrà eseguito solo su alcuni di essi, non è consigliabile usare un file di host predefinito, perché i nodi che verranno allocati al processo non sono noti. In questo caso, scrivere un wrapper di script bash per il comando **mpirun** per creare automaticamente il file host. È possibile trovare un wrapper di script bash di esempio denominato hpcimpirun.sh nella sezione Appendice alla fine di questo articolo e salvarlo come /openfoam/hpcimpirun.sh. Questo script di esempio esegue queste operazioni:

    1.	Configura le variabili di ambiente per **mpirun** e alcuni parametri di comando aggiuntivi per eseguire il processo MPI attraverso la rete RDMA. In questo caso, imposta quanto segue:

        *	I\_MPI\_FABRICS=shm:dapl
        *	I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0
        *	I\_MPI\_DYNAMIC\_CONNECTION=0

    2.	Crea un file host in base alla variabile di ambiente $CCP\_NODES\_CORES, che viene impostata dal nodo head HPC quando viene attivato il processo.

        Il formato della variabile $CCP\_NODES\_CORES segue questo modello:

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        dove

        * `<Number of nodes>`: è il numero di nodi allocato a questo processo.  
        
        * `<Name of node_n_...>`: è il nome di ogni nodo allocato a questo processo.
        
        * `<Cores of node_n_...>`: è il numero di core nel nodo allocato a questo processo.

        Ad esempio, se per l'esecuzione del processo sono necessari 2 core, il formato di $CCP\_NODES\_CORES sarà simile a:
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.	Chiama il comando **mpirun** e aggiunge 2 parametri nella riga di comando.

        * `--hostfile <hostfilepath>: <hostfilepath>` è il percorso del file host creato dallo script

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` è una variabile di ambiente impostata dal nodo head HPC Pack, che archivia il numero totale di core allocati a questo processo. In questo caso specifica il numero di processi per **mpirun**.


## Inviare un processo OpenFOAM

A questo punto è possibile inviare un processo in HPC Cluster Manager. Sarà quindi necessario passare lo script hpcimpirun.sh nelle righe di comando per alcune delle attività del processo.

1. Connettersi al nodo head del cluster e avviare HPC Cluster Manager.

2. In **Resource Management** assicurarsi che lo stato dei nodi di calcolo Linux sia **Online**. Se lo stato è diverso, selezionarli e fare clic su **Bring Online**.

3.  In **Job Management** fare clic su **New Job**.

4.  Immettere un nome per il processo, ad esempio _sloshingTank3D_.

    ![Dettagli del processo][job_details]

5.	In **Job resources** scegliere "Node" come tipo di risorsa e impostare il valore di Minimum su 2. In questo modo il processo verrà eseguito su 2 nodi Linux che, in questo esempio, presentano 8 core ciascuno.

    ![Risorse del processo][job_resources]

6.	Aggiungere al processo 4 attività con le righe di comando e le impostazioni seguenti per le attività.

    >[AZURE.NOTE]L'esecuzione di `source /openfoam/settings.sh` configura gli ambienti di runtime per OpenFOAM e MPI, quindi ognuna delle attività seguenti chiama questo script prima di eseguire il comando OpenFOAM.

    *   **Attività 1**. Eseguire **decomposePar** per generare i file di dati per l'esecuzione di **interDyMFoam** in parallelo.
    
        *   Assegnare all'attività 1 nodo

        *   **Riga di comando** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Directory di lavoro** - /openfoam/sloshingTank3D

        ![Dettagli dell'attività 1][task_details1]

    *   **Attività 2**. Eseguire **interDyMFoam** in parallelo per calcolare l'esempio.

        *   Assegnare all'attività 2 nodi

        *   **Riga di comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Directory di lavoro** - /openfoam/sloshingTank3D

        ![Dettagli dell'attività 2][task_details2]

    *   **Attività 3**. Eseguire **reconstructPar** per unire i set delle directory temporali da ogni directory processore\_N\_ in un singolo set di directory temporali.

        *   Assegnare all'attività 1 nodo

        *   **Riga di comando** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Directory di lavoro** - /openfoam/sloshingTank3D

        ![Dettagli dell'attività 3][task_details3]

    *   **Attività 4**. Eseguire **foamToEnsight** in parallelo per convertire i file dei risultati di OpenFOAM nel formato EnSight e posizionare i file EnSight in una directory denominata Ensight nella directory case.

        *   Assegnare all'attività 2 nodi

        *   **Riga di comando** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Directory di lavoro** - /openfoam/sloshingTank3D

        ![Dettagli dell'attività 4][task_details4]

6.	Aggiungere a queste attività le dipendenze in ordine di attività crescente.

    ![Dipendenze dell'attività][task_dependencies]

7.	Fare clic su **Submit** per eseguire il processo.

    Per impostazione predefinita, HPC Pack invia il processo usando l'account utente attualmente connesso. Dopo aver fatto clic su **Submit**, potrebbe essere visualizzata una finestra di dialogo richiede l'immissione del nome utente e della password.

    ![Credenziali del processo][creds]

    In alcune condizioni, HPC Pack ricorda le informazioni utente inserite in precedenza e non visualizza questa finestra di dialogo. Per fare in modo che HPC Pack la mostri di nuovo, immettere le informazioni seguenti in una finestra del prompt dei comandi e quindi inviare il processo.

    ```
    hpccred delcreds
    ```

8.	Il processo richiede da alcuni minuti a diverse ore in base ai parametri impostati per l'esempio. Nella mappa termica verrà visualizzato il processo in esecuzione in 2 nodi Linux.

    ![Mappa termica][heat_map]

    In ogni nodo vengono avviati 8 processi.

    ![Processi Linux][linux_processes]

9.  Al termine del processo, i risultati del processo saranno disponibili in C:\\OpenFoam\\sloshingTank3D e i file di log in C:\\OpenFoam.


## Visualizzare i risultati in EnSight

Facoltativamente è possibile usare [EnSight](https://www.ceisoftware.com/) per visualizzare e analizzare i risultati del processo OpenFOAM. Per altre informazioni sulla visualizzazione e l'animazione in EnSight, vedere questa [guida video](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Dopo aver installato EnSight nel nodo head, avviarlo.

2.  Aprire C:\\OpenFoam\\sloshingTank3D\\EnSight\\sloshingTank3D.case.

    Nel visualizzatore verrà visualizzato un serbatoio.

    ![Serbatoio in EnSight][tank]

3.	Creare un oggetto **Isosurface** da **internalMesh** e quindi scegliere la variabile **alpha\_water**.

    ![Creare un oggetto isosurface][isosurface]

4.	Impostare il colore per l'oggetto **Isosurface\_part** creato nel passaggio precedente. Ad esempio, impostarlo su azzurro.

    ![Modificare il colore dell'oggetto isosurface][isosurface_color]

5.  Creare un oggetto **Iso-volume** da **walls** selezionando **walls** nel riquadro **Parts** e fare clic sul pulsante **Isosurfaces** sulla barra degli strumenti.

6.	Nella finestra di dialogo selezionare **Type** per **Isovolume** e impostare l'opzione Min di **Isovolume range** su 0,5. Fare clic su **Create with selected parts** per creare l'oggetto isovolume.

7.	Impostare il colore per l'oggetto **Iso\_volume\_part** creato nel passaggio precedente. Ad esempio, impostarlo su blu.

8.	Impostare il colore per **walls**. Ad esempio, impostarlo su bianco trasparente.

9. Fare clic su **Play** per visualizzare i risultati della simulazione.

    ![Risultato per il serbatoio][tank_result]


## Appendice


### File cred.xml di esempio

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### File silent.cfg di esempio

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no

```

### Script settings.sh di esempio

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```

### Comandi di esempio per aggiungere repository e pacchetti dipendenti nei nodi Linux

```
sudo zypper ar ftp://ftp.muug.mb.ca/mirror/opensuse/factory-snapshot/repo/oss/ update1

sudo zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ update2

sudo zypper ar ftp://ftp.pbone.net/mirror/ftp.opensuse.org/factory-snapshot/repo/oss/ update3

sudo zypper ar ftp://mirror.switch.ch/pool/4/mirror/opensuse/opensuse/distribution/13.2/repo/oss/ update4

sudo zypper ar ftp://bo.mirror.garr.it/pub/1/opensuse/distribution/13.2/repo/oss/ update6

sudo zypper ar ftp://ftp.pbone.net/mirror/ftp.opensuse.org/distribution/13.2/repo/oss/ update7

sudo zypper ar ftp://ftp.icm.edu.pl/vol/rzm5/linux-opensuse/distribution/13.2/repo/oss/ update8

sudo zypper install -t pattern devel_C_C++

sudo zypper install cmake boost-devel gnuplot mpfr-devel openmpi-devel glu-devel  
```

###Script hpccharmrun.sh di esempio

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
	${MPIRUN} $*
else
	# Create the hostfile file
	NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

	# Get every node name and write into the hostfile file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the mpirun with hostfile arg
	${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details1.png
[task_details2]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details2.png
[task_details3]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details3.png
[task_details4]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_details4.png
[task_dependencies]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-cluster-hpcpack-openfoam/linux_processes.png

<!---HONumber=AcomDC_1125_2015-->