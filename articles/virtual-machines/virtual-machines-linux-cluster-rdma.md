<properties
 pageTitle="Configurare un cluster Linux RDMA per eseguire applicazioni MPI | Microsoft Azure"
 description="Scoprire come creare un cluster Linux di macchine virtuali di dimensioni A8 o A9 per utilizzare RDMA per eseguire app MPI."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="07/17/2015"
 ms.author="danlep"/>

# Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI

In questo articolo viene illustrato come configurare un cluster Linux RDMA in Azure con [macchine virtuali di dimensioni A8 e A9](virtual-machines-a8-a9-a10-a11-specs.md) per eseguire applicazioni MPI (Message Passing Interface) parallele. Quando si configurano macchine virtuali di dimensioni A8 e A9 basate su Linux per eseguire un'implementazione MPI supportata, le applicazioni MPI comunicano in modo efficiente su una rete a bassa latenza e a elevata velocità effettiva in Azure che è basata sulla tecnologia di accesso diretto a memoria remota (RDMA).

>[AZURE.NOTE]Azure Linux RDMA è attualmente supportata con Intel MPI Library versione 5.0 in esecuzione su SUSE Linux Enterprise Server 12 (SLES 12).
>
> Azure fornisce inoltre istanze A10 e A11 con utilizzo intensivo di calcolo, che hanno capacità di elaborazione identiche alle istanze A8 e A9, ma non dispongono di una connessione a una rete RDMA di back-end. Per eseguire carichi di lavoro MPI in Azure, in genere si otterranno prestazioni migliori con le istanze A8 e A9.


## Opzioni di distribuzione del cluster Linux

Di seguito vengono riportati i metodi utilizzabili per creare un cluster Linux RDMA con o senza un'utilità di pianificazione del processo.

* **HPC Pack**: è possibile creare un cluster Microsoft HPC Pack in Azure e aggiungere nodi di calcolo che eseguono distribuzioni Linux supportate (supportate a partire da HPC Pack 2012 R2 Update 2). Alcuni nodi Linux possono essere configurati per l'accesso alla rete RDMA. Per iniziare, vedere la [documentazione HPC Pack](http://go.microsoft.com/fwlink/?LinkId=617894).

* **Script dell'interfaccia della riga di comando di Azure**: è possibile utilizzare l'[interfaccia della riga di comando di Azure](../xplat-cli.md) in Mac, Linux e Windows per creare script personali per distribuire una rete virtuale e altri componenti necessari per creare un cluster Linux. L'interfaccia della riga di comando in modalità Gestione dei servizi di Azure consentirà di distribuire i nodi del cluster in modo seriale, pertanto la distribuzione di molti nodi di calcolo potrebbe richiedere alcuni minuti. Vedere i passaggi descritti nella parte restante di questo articolo per un esempio.

* **Modelli di Gestione risorse di Azure**: creando un semplice file del modello JSON di Gestione risorse di Azure ed eseguendo i comandi in modalità ARM dell'interfaccia della riga di comando di Azure o utilizzando il portale di anteprima di Azure, è possibile distribuire più macchine virtuali Linux A8 e A9 e definire reti virtuali, indirizzi IP statici, impostazioni DNS e altre risorse per creare un cluster di elaborazione in grado di sfruttare la rete RDMA ed eseguire i carichi di lavoro MPI. Per distribuire la soluzione desiderata, è possibile [creare un modello personale](../resource-group-authoring-templates.md) o consultare la [pagina dei modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/) per i modelli forniti da Microsoft o dalla community. I modelli di Gestione risorse in genere rappresentano il modo più veloce e affidabile per distribuire un cluster Linux.

## Distribuzione in Gestione dei servizi di Azure con gli script dell'interfaccia della riga di comando di Azure

I passaggi seguenti consentiranno di utilizzare l'interfaccia della riga di comando di Azure per distribuire una macchina virtuale SLES 12, installare Intel MPI Library e altre personalizzazioni, creare un'immagine della macchina virtuale personalizzata, quindi lo script della distribuzione di un cluster di macchine virtuali A8 o A9.

### Prerequisiti

* **Computer client**: sarà necessario un computer cliente basato su Mac, Linux o Windows per comunicare con Azure.

* **Sottoscrizione Azure**: se non si dispone di un account, è possibile creare un account di prova gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Quota di core**: potrebbe essere necessario aumentare la quota di core per distribuire un cluster di macchine virtuali A8 o A9. Ad esempio, se si desidera distribuire macchine virtuali A9 8 come illustrato in questo articolo, sarà necessario disporre di almeno 128 core. Per aumentare una quota, [aprire una richiesta di assistenza clienti online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

* **Interfaccia della riga di comando di Azure**: [installare](../xplat-cli-install.md) l'interfaccia della riga di Comando di Azure e [configurarla](../xplat-cli-connect.md) per utilizzare la sottoscrizione di Azure sul computer client.


### Provisioning di una macchina virtuale SLES 12

Dopo aver effettuato l'accesso ad Azure con l'interfaccia della riga di comando di Azure, eseguire il comando `azure config list` per verificare che l'interfaccia della riga di comando sia in modalità di Gestione dei servizi di Azure. Se non lo è, impostare la modalità eseguendo questo comando:

```
azure config mode asm
```

Digitare il comando seguente per elencare tutte le sottoscrizioni per le quali si dispone dell'autorizzazione:

```
azure account list
```

La sottoscrizione attiva corrente verrà identificata con l'opzione `Current` impostata su `true`. Se non è la sottoscrizione che si desidera utilizzare per creare il cluster, impostare il numero di sottoscrizione appropriato come sottoscrizione attiva:

```he
azure account set <subscription-number>
```

Per visualizzare le immagini SLES 12 HPC disponibili pubblicamente in Azure, eseguire un comando simile al seguente, se l'ambiente shell supporta **grep**:

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]Le immagini SLES 12 HPC sono preconfigurate con i driver Linux RDMA necessari per Azure.

Ora eseguire il provisioning di una macchina virtuale di dimensioni A9 con un'immagine 12 SLES HPC disponibile utilizzando un comando simile al seguente:

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 10004 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

dove

* le dimensioni (A9 in questo esempio) possono essere A8 o A9

* il numero di porta SSH esterna (10004 in questo esempio) è qualsiasi numero di porta valido; il numero di porta SSH interna verrà impostato su 22

* verrà creato un nuovo servizio cloud nell'area di Azure specificata dalla posizione; specificare una posizione "Stati Uniti occidentali" in cui sono disponibili le istanze A8 e A9

* attualmente il nome dell'immagine può essere `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (gratuita) o `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` per il supporto SUSE prioritario (a pagamento)

### Personalizzazione della macchina virtuale

Quando la macchina virtuale completa il provisioning, assegnare una porta SSH alla macchina virtuale utilizzando l'indirizzo IP esterno della macchina virtuale (o nome DNS) e il numero della porta esterna configurata e personalizzarla. Per informazioni dettagliate sulla connessione, vedere [Come accedere a una macchina virtuale che esegue Linux](virtual-machines-linux-how-to-log-on.md).

>[AZURE.NOTE]Microsoft Azure non fornisce l'accesso alla directory principale per le macchine virtuali Linux. Per ottenere l'accesso amministrativo quando si è connessi come utente è possibile utilizzare `sudo –s`.

**Aggiornamenti**: è possibile installare gli aggiornamenti utilizzando **zypper** e le utilità NFS.

>[AZURE.IMPORTANT]A questo punto è consigliabile non applicare gli aggiornamenti del kernel, che possono causare problemi con i driver Linux RDMA.

**Intel MPI**: scaricare e installare il runtime di Intel MPI Library 5.0 dal [sito Intel.com](https://software.intel.com/it-it/intel-mpi-library/). Dopo aver effettuato la registrazione a Intel, fare clic sul collegamento contenuto nel messaggio di posta elettronica di conferma alla relativa pagina web e copiare il collegamento di download del file .tgz per la versione appropriata di Intel MPI.

Eseguire comandi simili ai seguenti per installare Intel MPI sulla macchina virtuale:

```

$ wget <download link for your registration>
$ tar xvzf <tar-file>
$ cd <mpi-directory>
$ sudo ./install.sh
```

**Blocco della memoria**: affinché i codici MPI blocchino la memoria disponibile per RDMA, è necessario aggiungere o modificare le impostazioni seguenti nel file /etc/security/limits.conf:

```
<User or group name> hard    memlock <memory required for your application in KB>
<User or group name> soft    memlock <memory required for your application in KB>
```

>[AZURE.NOTE]A scopo di test, è inoltre possibile impostare memlock su illimitato. Ad esempio: `<User or group name>    hard    memlock unlimited`.


**Chiavi SSH**: stabilire l'attendibilità del nome utente e della password utilizzati per creare questa macchina virtuale tra tutti i nodi di calcolo nel cluster. Utilizzare il comando seguente per creare nuove chiavi SSH:

```
$ ssh-keygen
```

Salvare la chiave pubblica in un percorso predefinito e ricordare la passphrase immessa.

```
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Nella directory \~/.ssh modificare o creare il file ssh\_config. Fornire l'intervallo di indirizzi IP della rete privata che verrà utilizzato in Azure:

```
host 10.32.0.*
StrictHostKeyChecking no
```

In alternativa, è possibile elencare l'indirizzo IP della rete privata di ciascuna macchina virtuale del cluster come segue:

```
host 10.32.0.1
 StrictHostKeyChecking no
host 10.32.0.2
 StrictHostKeyChecking no
host 10.32.0.3
 StrictHostKeyChecking no
```

>[AZURE.NOTE]La configurazione di `StrictHostKeyChecking no` può creare un potenziale rischio di sicurezza se un indirizzo IP specifico o un intervallo non viene specificato come illustrato in precedenza.

**Applicazioni**: copiare tutte le applicazioni necessarie su questa macchina virtuale o eseguire altre personalizzazioni prima di acquisire l'immagine.

### Acquisire l'immagine

Per acquisire l'immagine, è necessario innanzitutto utilizzare il comando seguente nella macchina virtuale Linux:

```
$ sudo waagent -deprovision
```

Quindi, dal computer client, utilizzare i comandi dell'interfaccia della riga di comando di Azure seguenti per acquisire l'immagine. Per informazioni dettagliate, vedere [Come acquisire una macchina virtuale Linux da utilizzare come modello](virtual-machines-linux-capture-image.md)

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Dopo aver eseguito questi comandi, verrà acquisita l'immagine della macchina virtuale per l'utilizzo e la macchina virtuale verrà eliminata. Ora si dispone dell'immagine personalizzata pronta per distribuire un cluster.

### Distribuzione di un cluster con l'immagine

Modificare lo script seguente con i valori appropriati per l'ambiente ed eseguirlo dal computer client. Poiché il metodo di distribuzione di Gestione dei servizi di Azure consente di distribuire le macchine virtuali in modo seriale, la distribuzione delle macchine virtuali A8 e A9 suggerite in questo script richiederà alcuni minuti.

```
### Script to create a compute cluster without a scheduler in a VNet in Azure
### Create a custom private network in Azure
### Replace 10.32.0.0 with your virtual network address space
### Replace <network-name> with your network identifier
### Select a region where A8 and A9 VMs are available, such as West US
### See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" –e 10.32.0.0 <network-name>

### Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
### Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

### Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

### Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

### In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i <image-name>
done

### Save this script and run it at the CLI prompt to provision your cluster
```

## Configurazione ed esecuzione di Intel MPI

Per eseguire applicazioni MPI su Azure Linux RDMA, è necessario configurare determinate variabili di ambiente specifiche di Intel MPI. Di seguito viene riportato uno script di esempio per configurare le variabili ed eseguire un'applicazione.

```
#!/bin/bash -x
source /opt/intel/impi_latest/bin64/mpivars.sh

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

Il formato del file host è indicato di seguito. Aggiungere una riga per ciascun nodo del cluster. Specificare gli indirizzi IP privati dalla rete virtuale definita in precedenza, non i nomi DNS.

```
private ip address1:16 [e.g. 10.32.0.1:16]
private ip address2:16
...
```

## Verifica di un cluster a due nodi di base dopo l'installazione di Intel MPI

È possibile eseguire i comandi Intel MPI seguenti per verificare la configurazione del cluster utilizzando un benchmark pingpong.

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>, <host2> -ppn 1 -n 2 -env I_MPI_FABRICS dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
```

In un cluster di lavoro con due nodi verrà visualizzato un output simile al seguente:

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

## Considerazioni sulla topologia di rete

* Nelle macchine virtuali Linux, Eth1 è riservato per il traffico di rete RDMA. Non modificare le impostazioni di Eth1 o le informazioni nel file di configurazione riferite a questa rete.

* In Azure IP over Infiniband (IB) non è supportato. Solo RDMA over IB è supportato.

* Nelle macchine virtuali Linux, Eth0 è riservato per il normale traffico di rete di Azure.

## Passaggi successivi

* Provare a distribuire e a eseguire le applicazioni MPI Linux nel cluster Linux.

* Per le linee guida su Intel MPI, vedere la [documentazione Intel MPI Library](https://software.intel.com/it-it/articles/intel-mpi-library-documentation/).

<!---HONumber=August15_HO6-->