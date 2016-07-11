<properties
 pageTitle="Cluster Linux RDMA per l'esecuzione di applicazioni MPI | Microsoft Azure"
 description="Creare un cluster Linux di VM di dimensioni A8 o A9 per usare RDMA per eseguire applicazioni MPI."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="05/09/2016"
 ms.author="danlep"/>

# Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Informazioni su come configurare un cluster Linux RDMA in Azure con [macchine virtuali di dimensioni A8 e A9](virtual-machines-linux-a8-a9-a10-a11-specs.md) per eseguire applicazioni MPI (Message Passing Interface) in parallelo. Quando si configura un cluster di VM con dimensioni A8 e A9 per eseguire una distribuzione Linux HPC supportata e un'implementazione MPI supportata, le applicazioni MPI comunicano in modo efficiente su una rete a bassa latenza e a elevata velocità effettiva in Azure basata sulla tecnologia di Accesso diretto a memoria remota (RDMA).

>[AZURE.NOTE] Azure Linux RDMA è attualmente supportato con Intel MPI Library versione 5 in esecuzione su VM con dimensioni A8 o A9 create da un'immagine HPC SUSE Linux Enterprise Server (SLES) 12 o un'immagine HPC 6.5 o 7.1 basata su CentOS in Azure Marketplace. Le immagini del Marketplace HPC basate su CentOS installano Intel MPI versione 5.1.3.181.



## Opzioni di distribuzione del cluster

Di seguito vengono riportati i metodi utilizzabili per creare un cluster Linux RDMA con o senza un'utilità di pianificazione del processo.

* **HPC Pack**: creare un cluster Microsoft HPC Pack in Azure e aggiungere nodi di calcolo di dimensioni A8 e A9 che eseguono distribuzioni Linux supportate per accedere alla rete RDMA. Vedere l'articolo di [introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

* **Script dell'interfaccia della riga di comando di Azure**: come illustrato nei restanti passaggi di questo articolo, usare l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) per creare script per la distribuzione di una rete virtuale e degli altri componenti necessari per la creazione di un cluster di VM Linux di dimensioni A8 e A9. L'interfaccia della riga di comando in modalità Azure Service Management crea i nodi del cluster in modo seriale, quindi se si distribuiscono molti nodi di calcolo il completamento della distribuzione potrebbe richiedere alcuni minuti.

* **Modelli di Azure Resource Manager**: usare il modello di distribuzione Resource Manager per distribuire più VM Linux A8 e A9 e definire reti virtuali, indirizzi IP statici, impostazioni DNS e altre risorse per un cluster di elaborazione in grado di sfruttare la rete RDMA per eseguire i carichi di lavoro MPI. Per distribuire la soluzione desiderata, è possibile [creare un modello personalizzato](../resource-group-authoring-templates.md) o vedere la [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) per accedere ai modelli forniti da Microsoft o dalla community. I modelli di Gestione risorse riescono a fornire un modo veloce e affidabile per distribuire un cluster Linux.

## Distribuzione di esempio nel modello classico

I passaggi seguenti consentono l'uso dell'interfaccia della riga di comando di Azure per distribuire una VM HPC SUSE Linux Enterprise Server 12 da Azure Marketplace, installare Intel MPI Library e altre personalizzazioni, creare un'immagine di VM personalizzata e quindi lo script per la distribuzione di un cluster di VM A8 o A9.

>[AZURE.TIP]  Usare una procedura simile per distribuire un cluster di VM A8 o A9 dall'immagine HPC 6.5 o 7.1 basata su CentOS in Azure Marketplace. Le differenze sono indicate nella procedura. Ad esempio, poiché le immagini HPC basate su CentOS includono Intel MPI, non è necessario installare Intel MPI separatamente nelle VM create da queste immagini.

### Prerequisiti

*   **Computer client**: sarà necessario un computer client basato su Mac, Linux o Windows per comunicare con Azure. Nella procedura si presuppone che venga usato un client Linux.

*   **Sottoscrizione di Azure**: se non è disponibile una sottoscrizione, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti. Per cluster di maggiori dimensioni, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto.

*   **Quota di core**: potrebbe essere necessario aumentare la quota di core per distribuire un cluster di macchine virtuali A8 o A9. Ad esempio, se si desidera distribuire macchine virtuali A9 8 come illustrato in questo articolo, sarà necessario disporre di almeno 128 core. Per aumentare una quota, è possibile [aprire una richiesta di assistenza clienti online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza alcun addebito.

*   **Interfaccia della riga di comando di Azure**: [installare](../xplat-cli-install.md) l'interfaccia della riga di comando di Azure e [connetterla alla sottoscrizione di Azure](../xplat-cli-connect.md) dal computer client.

*   **Intel MPI**: per personalizzare un'immagine di VM HPC SLES 12 per il cluster (vedere i dettagli più avanti in questo articolo), è necessario scaricare e installare un runtime di Intel MPI Library 5 corrente dal [sito Intel.com](https://software.intel.com/it-IT/intel-mpi-library/). Come preparazione, dopo aver eseguito la registrazione a Intel, fare clic sul collegamento contenuto nel messaggio di posta elettronica di conferma per accedere alla relativa pagina Web e copiare il collegamento di download del file con estensione tgz per la versione appropriata di Intel MPI. Questo articolo si riferisce a Intel MPI versione 5.0.3.048.

    >[AZURE.NOTE] Se si usa l'immagine HPC CentOS 6.5 o CentOS 7.1 in Azure Marketplace per creare i nodi del cluster, Intel MPI versione 5.1.3.181 è preinstallato automaticamente nella VM.

### Provisioning di una macchina virtuale SLES 12

Dopo l'accesso ad Azure con l'interfaccia della riga di comando di Azure, eseguire `azure config list` per verificare che l'output indichi la modalità Azure Service Management. Se non lo è, impostare la modalità eseguendo questo comando:


    azure config mode asm


Digitare il comando seguente per elencare tutte le sottoscrizioni per le quali si dispone dell'autorizzazione:


    azure account list

La sottoscrizione attualmente attiva sarà identificata da `Current` impostato su `true`. Se non è la sottoscrizione che si desidera utilizzare per creare il cluster, impostare il numero di sottoscrizione appropriato come sottoscrizione attiva:

    azure account set <subscription-number>

Per visualizzare le immagini SLES 12 HPC disponibili pubblicamente in Azure, eseguire un comando simile al seguente, presupponendo che l'ambiente shell supporti **grep**:


    azure vm image list | grep "suse.*hpc"

Ora eseguire il provisioning di una macchina virtuale di dimensioni A9 con un'immagine 12 SLES HPC disponibile utilizzando un comando simile al seguente:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

dove

* le dimensioni (A9 in questo esempio) possono essere A8 o A9

* il numero della porta SSH esterna (22 in questo esempio, ovvero la porta SSH predefinita) può essere qualsiasi numero di porta valido; il numero della porta SSH interna verrà impostato su 22

* verrà creato un nuovo servizio cloud nell'area di Azure specificata dalla posizione; specificare una posizione "Stati Uniti occidentali" in cui sono disponibili le istanze A8 e A9

* attualmente il nome dell'immagine SLES 12 può essere `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` o `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` per il supporto SUSE prioritario (a pagamento)

    >[AZURE.NOTE]Se si vuole usare un'immagine HPC basata su CentOS, i nomi correnti delle immagini sono `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` e `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`.

### Personalizzazione della macchina virtuale

Quando la macchina virtuale completa il provisioning, assegnare una porta SSH alla macchina virtuale utilizzando l'indirizzo IP esterno della macchina virtuale (o nome DNS) e il numero della porta esterna configurata e personalizzarla. Per informazioni dettagliate sulla connessione, vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-classic-log-on.md). È consigliabile eseguire i comandi come l'utente configurato nella macchina virtuale, a meno che non sia necessario l'accesso alla radice per eseguire un passaggio.

>[AZURE.IMPORTANT]Microsoft Azure non fornisce l'accesso alla directory principale per le macchine virtuali Linux. Per ottenere l'accesso amministrativo quando si è connessi come utente alla macchina virtuale, eseguire i comandi con `sudo`.

* **Aggiornamenti**: installare gli aggiornamenti usando **zypper**. È anche possibile decidere di installare le utilità NFS.

    >[AZURE.IMPORTANT]Se è stata distribuita una VM HPC SLES 12, a questo punto è consigliabile non applicare aggiornamenti del kernel, che possono causare problemi con i driver Linux RDMA.
    >
    >Nelle immagini HPC basate su CentOS dal Marketplace gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum**. In questo modo i driver Linux RDMA vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.

* **Aggiornamenti dei driver Linux RDMA**: se è stata distribuita una VM HPC SLES 12, sarà necessario aggiornare i driver RDMA. Per dettagli, vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](virtual-machines-linux-a8-a9-a10-a11.md#Linux-RDMA-driver-updates-for-SLES-12).

* **Intel MPI**: se è stata distribuita una VM HPC SLES 12, scaricare e installare il runtime di Intel MPI Library 5 dal sito Intel.com eseguendo comandi simili ai seguenti. Questo passaggio non è necessario se è stata distribuita una VM HPC 6.5 o 7.1 basata su CentOS.

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    Accettare le impostazioni predefinite per installare Intel MPI nella macchina virtuale.

* **Blocco della memoria**: affinché i codici MPI blocchino la memoria disponibile per RDMA, è necessario aggiungere o modificare le impostazioni seguenti nel file /etc/security/limits.conf. Per modificare questo file sarà necessario l'accesso alla radice. Se è stato distribuito una VM HPC 6.5 o 7.1 basata su CentOS, le impostazioni sono già aggiunte al file.

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]A scopo di test, è inoltre possibile impostare memlock su illimitato. Ad esempio: `<Nome utente o gruppo> hard memlock unlimited.

* **Chiavi SSH per VM SLES 12**: generare chiavi SSH per stabilire una relazione di trust per il proprio account utente tra tutti i nodi di calcolo del cluster HPC SLES 12 quando vengono eseguiti processi MPI. Se è stata distribuita una VM HPC basata su CentOS, non seguire questo passaggio. Vedere le istruzioni più avanti nell'articolo per configurare un trust SSH senza password tra i nodi del cluster dopo aver acquisito l'immagine e distribuito il cluster.

    Usare il comando seguente per creare chiavi SSH. È sufficiente premere INVIO per generare le chiavi nel percorso predefinito senza impostare una passphrase.

        ssh-keygen

    Aggiungere la chiave pubblica alla fine del file authorized\_keys per le chiavi pubbliche note.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Nella directory ~/.ssh modificare o creare il file "config". Specificare l'intervallo di indirizzi IP della rete privata da usare in Azure (10.32.0.0/16 in questo esempio):

        host 10.32.0.*
        StrictHostKeyChecking no

    In alternativa, elencare l'indirizzo IP di rete privata di ogni macchina virtuale del cluster come segue:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]La configurazione di `StrictHostKeyChecking no` può generare potenziali rischi per la sicurezza se un indirizzo IP o un intervallo specifico non viene specificato come mostrato in questi esempi.

* **Applicazioni**: installare le applicazioni necessarie in questa macchina virtuale o eseguire altre personalizzazioni prima di acquisire l'immagine.

### Acquisire l'immagine

Per acquisire l'immagine, eseguire prima il comando seguente nella macchina virtuale Linux. In questo modo si esegue il deprovisioning della macchina virtuale, ma vengono mantenuti gli account utente e le chiavi SSH configurati.

```
sudo waagent -deprovision
```

Quindi, dal computer client, utilizzare i comandi dell'interfaccia della riga di comando di Azure seguenti per acquisire l'immagine. Per informazioni dettagliate, vedere [Come acquisire una macchina virtuale Linux classica come immagine](virtual-machines-linux-classic-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Dopo l'esecuzione di questi comandi, l'immagine della macchina virtuale viene acquisita per l'uso e la macchina virtuale viene eliminata. Ora si dispone dell'immagine personalizzata pronta per distribuire un cluster.

### Distribuzione di un cluster con l'immagine

Modificare lo script Bash seguente con i valori appropriati per il proprio ambiente ed eseguirlo dal computer client. Poiché Azure distribuisce le VM in modo seriale nel modello di distribuzione classica, sono necessari alcuni minuti per distribuire le VM A8 o A9 suggerite in questo script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## Trust SSH senza password in un cluster CentOS

Se è stato distribuito un cluster con un'immagine HPC basata su CentOS, sono disponibili due metodi per stabilire relazioni di trust tra i nodi di calcolo: autenticazione basata sull'host e autenticazione basata sull'utente. L'autenticazione basata sull'host non rientra nell'ambito di questo articolo e in genere deve essere eseguita tramite uno script di estensione durante la distribuzione. L'autenticazione basata sull'utente è utile per stabilire un trust dopo la distribuzione e richiede la generazione e la condivisione di chiavi SSH tra i nodi di calcolo nel cluster. Questo approccio è comunemente noto come accesso SSH senza password ed è obbligatorio quando si eseguono processi MPI.

In [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) è disponibile uno script di esempio fornito dalla community per abilitare l'autenticazione utente semplice in un cluster HPC basato su CentOS. È possibile scaricare e usare questo script con la procedura seguente. È anche possibile modificare lo script o usare qualsiasi altro metodo per stabilire l'autenticazione SSH senza password tra i nodi di calcolo del cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Per eseguire lo script, è necessario conoscere il prefisso degli indirizzi IP della subnet. È possibile ottenerlo con il comando seguente in uno dei nodi del cluster. L'output sarà essere simile a 10.1.3.5 e il prefisso è la parte 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Eseguire lo script con tre parametri: il nome utente comune nei nodi di calcolo, la password comune per tale utente nei nodi di calcolo e il prefisso della subnet restituito dal comando precedente.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Lo script esegue queste operazioni:

* Crea una directory nel nodo host denominata SSH, necessaria per l'accesso senza password.
* Crea un file di configurazione nella directory SSH che indica di consentire l'accesso senza password da qualsiasi nodo del cluster.
* Crea file contenenti i nomi dei nodi e gli indirizzi IP dei nodi per tutti i nodi del cluster. Questi file vengono lasciati dopo l'esecuzione dello script come riferimento per l'utente.
* Crea una coppia di chiavi pubblica e privata per ogni nodo del cluster, incluso il nodo host, condivide le informazioni sulla coppia di chiavi e crea una voce nel file authorized\_keys.

>[AZURE.WARNING]L'esecuzione di questo script può creare un potenziale rischio per la sicurezza. Assicurarsi che le informazioni sulla chiave pubblica in ~/.ssh non vengano distribuite.


## Configurazione ed esecuzione di Intel MPI

Per eseguire applicazioni MPI su Azure Linux RDMA, è necessario configurare determinate variabili di ambiente specifiche di Intel MPI. Di seguito è riportato uno script Bash di esempio per configurare le variabili ed eseguire un'applicazione. Modificare il percorso in mpivars.sh secondo le esigenze per l'installazione di Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Il formato del file host è indicato di seguito. Aggiungere una riga per ciascun nodo del cluster. Specificare gli indirizzi IP privati dalla rete virtuale definita in precedenza, non i nomi DNS. Ad esempio, per due host con gli indirizzi IP 10.32.0.1 e 10.32.0.2 il file contiene quanto segue:

```
10.32.0.1:16
10.32.0.2:16
```

## Verifica di un cluster a due nodi di base dopo l'installazione di Intel MPI

Se non si è ancora provveduto, prima configurare l'ambiente per Intel MPI.

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### Eseguire un comando MPI semplice

Eseguire un comando MPI semplice su uno dei nodi di calcolo per vedere che MPI è installato correttamente e può comunicare tra almeno due nodi di calcolo. Il comando **mpirun** seguente esegue il comando **hostname** su due nodi.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
L'output dovrebbe elencare i nomi di tutti i nodi passati come input per `-hosts`. Ad esempio, un comando **mpirun** con due nodi restituirà un output simile al seguente:

```
cluster11
cluster12
```

### Eseguire un benchmark MPI

Il comando Intel MPI seguente verifica la configurazione del cluster e la connessione alla rete RDMA mediante un benchmark pingpong.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

In un cluster funzionante con due nodi dovrebbe venire visualizzato un output simile al seguente. Nella rete RDMA di Azure è prevista una latenza pari o inferiore a 3 microsecondi per i messaggi con una dimensione massima di 512 byte.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## Passaggi successivi

* Provare a distribuire e a eseguire le applicazioni MPI Linux nel cluster Linux.

* Per istruzioni su Intel MPI, vedere la [documentazione relativa a Intel MPI Library](https://software.intel.com/it-IT/articles/intel-mpi-library-documentation/).

* Provare un [modello di avvio rapido ](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) per creare un cluster Intel Lustre usando un'immagine basata HPC su CentOS.

<!---HONumber=AcomDC_0629_2016-->