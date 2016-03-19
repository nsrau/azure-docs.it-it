<properties
 pageTitle="NAMD con Microsoft HPC Pack su VM Linux | Microsoft Azure"
 description="Informazioni su come distribuire un cluster Microsoft HPC Pack in Azure e come eseguire una simulazione NAMD con charmrun in più nodi di calcolo Linux."
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
 ms.date="12/02/2015"
 ms.author="danlep"/>

# Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure

Questo articolo illustra come distribuire un cluster Microsoft HPC Pack su Azure con più nodi di calcolo Linux ed eseguire un processo [NAMD](http://www.ks.uiuc.edu/Research/namd/) con **charmrun** per calcolare e visualizzare la struttura di un grande sistema biomolecolare.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.



NAMD (ovvero programma Nanoscale Molecular Dynamics) è un pacchetto di dinamica molecolare parallela progettato per una simulazione a prestazioni elevate di grandi sistemi biomolecolari contenenti fino a milioni di atomi, ad esempio virus, strutture di celle e proteine di grandi dimensioni. NAMD è scalabile fino a centinaia di core per simulazioni tipiche e fino a più di 500.000 core per le simulazioni più grandi.

Microsoft HPC Pack fornisce le funzionalità necessarie per eseguire svariate applicazioni HPC e parallele su larga scala, incluse le applicazioni MPI, in cluster di macchine virtuali di Microsoft Azure. A partire da Microsoft HPC Pack 2012 R2 Update 2, HPC Pack supporta anche l'esecuzione di applicazioni HPC Linux su macchine virtuali del nodo di calcolo Linux distribuite in un cluster HPC Pack. Per una panoramica, vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md).


## Prerequisiti

* **Cluster HPC Pack con nodi di calcolo Linux** - Vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md) per informazioni sui prerequisiti e sui passaggi da eseguire per distribuire un cluster HPC Pack con nodi di calcolo Linux in Azure usando uno script di Azure PowerShell e immagini HPC Pack in Azure Marketplace.

    Di seguito è disponibile un file di configurazione XML di esempio, che può essere usato con lo script per distribuire un cluster HPC Pack basato su Azure, costituito da un nodo head Windows Server 2012 R2 e 4 nodi di calcolo CentOS 6.6 di tipo Grande (A3). Sostituire i valori dell'esempio con i valori appropriati per la sottoscrizione e i nomi dei servizi.

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>West US</Location>  
      <VNet>
        <VNetName>MyVNet</VNetName>
        <SubnetName>Subnet-1</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>CentOS66HN</VMName>
        <ServiceName>MyHPCService</ServiceName>
        <VMSize>Large</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
        <ServiceName>MyLnxCNService</ServiceName>
        <VMSize>Large</VMSize>
        <NodeCount>4</NodeCount>
        <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>    
```


* **Software e file per le esercitazioni NAMD** - Scaricare il software NAMD per Linux dal sito [NAMD](http://www.ks.uiuc.edu/Research/namd/). Questo articolo è basato su NAMD versione 2.10 e usa l'archivio [Linux-x86\_64 (Intel/AMD a 64 bit con Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310), che verrà usato per eseguire NAMD in più nodi di calcolo Linux in una rete di cluster. Scaricare anche i [file per le esercitazioni NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Seguire le istruzioni disponibili più avanti nell'articolo per estrarre l'archivio e gli esempi per le esercitazioni nel nodo head del cluster.

* **VMD** (facoltativo) - Per visualizzare i risultati del processo NAMD, scaricare e installare il programma di visualizzazione molecolare [VMD](http://www.ks.uiuc.edu/Research/vmd/) nel computer desiderato. La versione corrente è 1.9.2. Per iniziare, vedere il sito per il download di VMD.


## Configurare il trust reciproco tra i nodi di calcolo
L'esecuzione di un processo su più nodi Linux richiede una relazione di trust tra i nodi (mediante **rsh** o **ssh**). Quando si crea il cluster HPC Pack con lo script di distribuzione IaaS di Microsoft HPC Pack, lo script configura automaticamente un trust reciproco permanente per l'account amministratore specificato. Per gli utenti non amministratori creati nel dominio del cluster è necessario configurare una relazione di trust reciproco temporanea tra i nodi in caso di allocazione di un processo e quindi eliminare la relazione dopo il completamento del processo. Per eseguire questa operazione per ogni utente, fornire una coppia di chiavi RSA al cluster usato da HPC Pack per stabilire la relazione di trust.

### Generare una coppia di chiavi RSA
È possibile generare con facilità una coppia di chiavi RSA, che contiene una chiave pubblica e una chiave privata, eseguendo il comando **ssh-keygen** di Linux.

1.	Accedere a un computer Linux.

2.	Eseguire il comando indicato di seguito.

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE]Premere **INVIO** per usare le impostazioni predefinite fino al completamento del comando. Non immettere una passphrase. Quando viene richiesta una password, è sufficiente premere **INVIO**.

    ![Generare una coppia di chiavi RSA][keygen]

3.	Passare alla directory ~/.ssh. La chiave privata viene archiviata in id\_rsa e la chiave pubblica in id\_rsa.pub.

    ![Chiavi pubbliche e private][keys]

### Aggiungere la coppia di chiavi al cluster HPC Pack
1.	Stabilire una connessione Desktop remoto al nodo head con l'account amministratore di HPC Pack, ovvero l'account amministratore configurato durante l'esecuzione dello script di distribuzione.

2. Usare le procedure standard di Windows Server per creare un account utente di dominio nel dominio di Active Directory del cluster. Ad esempio, usare lo strumento Utenti e computer di Active Directory sul nodo head. Gli esempi in questo articolo presuppongono che venga creato un utente di dominio denominato hpclab\\hpcuser.

2.	Creare un file con nome C:\\cred.xml e copiarvi i dati della chiave RSA. Un esempio è disponibile nei file di esempio alla fine dell'articolo.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.	Aprire un prompt dei comandi e immettere il comando seguente per impostare i dati delle credenziali per l'account hpclab\\hpcuser. Usare il parametro **extendeddata** per passare il nome del file C:\\cred.xml creato per i dati della chiave.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Questo comando viene completato correttamente senza output. Dopo avere configurato le credenziali per gli account utente necessari per l'esecuzione dei progetti, archiviare il file cred.xml in un percorso sicuro oppure eliminarlo.

5.	Se la coppia di chiavi RSA è stata generata in uno dei nodi Linux, ricordare di eliminare le chiavi dopo averle usate. HPC Pack non configura il trust reciproco se rileva un file id\_rsa o id\_rsa.pub esistente.

>[AZURE.IMPORTANT]Non è consigliabile eseguire un processo di Linux come amministratore del cluster in un cluster condiviso, perché un processo inviato da un amministratore viene eseguito con l'account radice nei nodi Linux. Un processo inviato da un utente non amministratore viene eseguito con un account utente Linux locale, con lo stesso nome dell'utente del processo, e HPC Pack configura il trust reciproco per questo utente Linux in tutti i nodi allocati al processo. È possibile configurare manualmente l'utente Linux nei nodi Linux prima di eseguire il processo. In alternativa, HPC Pack crea automaticamente l'utente quando viene inviato il processo. Se HPC Pack crea l'utente, HPC Pack lo eliminerà dopo il completamento del processo. Le chiavi vengono rimosse dopo il completamento del processo sui nodi, per ridurre le minacce alla sicurezza.

## Configurare una condivisione di file per i nodi Linux

Configurare ora una condivisione SMB standard su una cartella nel nodo head, quindi montare la cartella condivisa in tutti i nodi Linux per consentire ai nodi Linux di accedere ai file NAMD con un percorso comune. Per informazioni sulle opzioni di condivisione e sui passaggi da eseguire, vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-cluster-hpcpack.md). È consigliabile montare una cartella condivisa nel nodo head in questo articolo, perché i nodi Linux CentOS 6.6 non supportano attualmente il servizio file di Azure, che offre funzionalità simili. Per altre informazioni sul montaggio di una condivisione di file di Azure, vedere il post di blog relativo alla [creazione di connessioni persistenti ai file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

1.	Creare una cartella sul nodo head e condividerla con tutti gli utenti, configurando privilegi di lettura/scrittura. In questo esempio \\\CentOS66HN\\Namd è il nome della cartella, dove CentOS66HN è il nome host del nodo head.

2. Estrarre i file NAMD nella cartella usando una versione Windows di **tar** o un'altra utilità Windows che gestisce gli archivi con estensione tar. Estrarre l'archivio tar NAMD in \\\CentOS66HN\\Namd\\namd2, quindi estrarre i file per le esercitazioni in \\\CentOS66HN\\Namd\\namd2\\namdsample.

2.	Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per montare la cartella condivisa.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Il primo comando crea una cartella denominata /namd2 su tutti i nodi del gruppo LinuxNodes. Il secondo comando monta la cartella condivisa //CentOS66HN/Namd/namd2 nella cartella con i bit dir\_mode e file\_mode impostati su 777. I valori *username* e *password* nel comando devono corrispondere alle credenziali di un utente nel nodo head.

>[AZURE.NOTE]Il simbolo "`" nel secondo comando è un simbolo di escape per PowerShell. "`," significa che "," (virgola) è una parte del comando.


## Preparare l'esecuzione di un processo NAMD

 Il processo NAMD necessita di un file *nodelist* per consentire a **charmrun** di conoscere il numero di nodi da usare quando vengono avviati i processi NAMD. È necessario scrivere uno script Bash che genera il file nodelist ed esegue **charmrun** con tale file nodelist. È quindi possibile inviare un processo NAMD in HPC Cluster Manager per chiamare questo script.

### Variabili di ambiente e file nodelist
Le informazioni sui nodi e sui core sono disponibili nella variabile di ambiente $CCP\_NODES\_CORES, che viene configurata automaticamente dal nodo head HPC Pack quando il processo viene attivato. Il formato della variabile $CCP\_NODES\_CORES è il seguente:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
```

Consente di elencare il numero totale di nodi, i nomi dei nodi e il numero di core in ogni nodo allocati al processo. Ad esempio, se per l'esecuzione del processo sono necessari 10 core, il valore di $CCP\_NODES\_CORES sarà simile a:

```
3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
```

Le informazioni seguenti sono disponibili nel file nodelist, che verrà generato dallo script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Ad esempio:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```
### Script Bash per la creazione di un file nodelist

Usando il proprio editor di testo preferito, creare lo script Bash seguente nella cartella contenente i file di programma NAMD e denominarlo hpccharmrun.sh. Un esempio completo è disponibile nei file di esempio alla fine dell'articolo. Questo script bash esegue le operazioni seguenti.

>[AZURE.TIP]Salvare lo script come file di testo con terminazioni riga Linux (solo LF, non CR LF), in modo da assicurarne l'esecuzione corretta nei nodi Linux.

1.	Definire alcune variabili.

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.	Ottenere le informazioni sul nodo dalle variabili di ambiente. $NODESCORES archivia un elenco di parole suddivise da $CCP\_NODES\_CORES. $COUNT corrisponde alle dimensioni di $NODESCORES.

    ```
    # Get node information from the environment variables
    # CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```

3.	Se la variabile $CCP\_NODES\_CORES non è configurata, avviare direttamente **charmrun**. È consigliabile seguire questa procedura solo quando si esegue lo script direttamente nei nodi Linux.

    ```
    if [ ${COUNT} -eq 0 ]
    then
    	# CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
    	#echo ${CHARMRUN} $*
    	${CHARMRUN} $*
    ```

4.	In alternativa, creare un file nodelist per **charmrun**.

    ```
    else
    	# Create the nodelist file
    	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    	# Write the head line
    	echo "group main" > ${NODELIST_PATH}

    	# Get every node name and number of cores and write into the nodelist file
    	I=1
    	while [ ${I} -lt ${COUNT} ]
    	do
    		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
    		let "I=${I}+2"
    	done
```
5.	Eseguire **charmrun** con il file nodelist, ottenere il relativo stato restituito e quindi rimuovere il file nodelist al termine delle operazioni.

    ${CCP\_NUMCPUS} è un'altra variabile di ambiente configurata dal nodo head HPC Pack. Archivia il numero totale di core allocati per questo processo. Viene usata per specificare il numero di processi per charmrun.

    ```
	# Run charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
    fi

    ```
6.	Uscire con lo stato restituito di **charmrun**.

    ```
    exit ${RTNSTS}
    ```

## Inviare un processo NAMD

È ora possibile inviare un processo NAMD in HPC Cluster Manager.

1.	Connettersi al nodo head del cluster e avviare HPC Cluster Manager.

2.  In **Node Management** assicurarsi che lo stato dei nodi di calcolo Linux sia **Online**. Se lo stato è diverso, selezionarli e fare clic su **Bring Online**.

2.  In **Job Management** fare clic su **New Job**.

3.	Immettere un nome per il processo, ad esempio *hpccharmrun*.

    ![Nuovo processo HPC][namd_job]

4.	Nella pagina **Job Details** in **Job Resources** selezionare **Node** come tipo di risorsa, quindi impostare il valore **Minimum** su 3. In questo esempio il processo verrà eseguito su tre nodi Linux e ogni nodo ha 4 core.

    ![Risorse del processo][job_resources]

5.	Nella pagina **Task Details and I/O Redirection** aggiungere una nuova attività al processo, quindi impostare i valori seguenti.

    * **Command line** - `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    * **Working directory** - /namd2

    * **Minimum** - 3

    ![Dettagli dell'attività][task_details]

    >[AZURE.NOTE]È necessario configurare qui la directory di lavoro, perché **charmrun** prova a passare alla stessa directory di lavoro in ogni nodo. Se la directory di lavoro non è configurata, HPC Pack avvia il comando in una cartella con nome casuale creata in uno dei nodi Linux. Ciò provoca l'errore seguente negli altri nodi: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Per evitare questo errore, specificare un percorso di cartella accessibile a tutti i nodi come directory di lavoro.

5.	Fare clic su **Submit** per eseguire il processo.

    Per impostazione predefinita, HPC Pack invia il processo usando l'account utente attualmente connesso. È possibile che una finestra di dialogo richieda l'immissione del nome utente e della password dopo la selezione di **Submit**.

    ![Credenziali del processo][creds]

    In alcune condizioni, HPC Pack ricorda le informazioni utente inserite in precedenza e non visualizza questa finestra di dialogo. Per fare in modo che HPC Pack la mostri di nuovo, immettere le informazioni seguenti in una finestra di comando, quindi inviare il processo.

    ```
    hpccred delcreds
    ```

6.	Il completamento del processo richiede alcuni minuti.

7.	Il log del processo è disponibile in \<headnodeName>\\Namd\\namd2\\namd2\_hpccharmrun.log e i file di output sono disponibili in \<headnode>\\Namd\\namd2\\namdsample\\1-2-sphere.

8.	Facoltativamente, avviare VMD per visualizzare i risultati del processo. I passaggi per la visualizzazione dei file di output NAMD (in questo caso, una molecola proteica di ubiquitina in una sfera d'acqua) non rientrano nell'ambito di questo articolo. Per informazioni dettagliate, vedere l'[esercitazione relativa a NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf).

    ![Risultati del processo][vmd_view]

## File di esempio

### Script hpccharmrun.sh di esempio

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
	#echo ${CHARMRUN} $*
	${CHARMRUN} $*
else
	# Create the nodelist file
	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

	# Write the head line
	echo "group main" > ${NODELIST_PATH}

	# Get every node name & cores and write into the nodelist file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 
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




<!--Image references-->
[keygen]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keygen.png
[keys]: ./media/virtual-machines-linux-cluster-hpcpack-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-cluster-hpcpack-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-cluster-hpcpack-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-cluster-hpcpack-namd/creds.png
[task_details]: ./media/virtual-machines-linux-cluster-hpcpack-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-cluster-hpcpack-namd/vmd_view.png

<!---HONumber=AcomDC_1210_2015-->