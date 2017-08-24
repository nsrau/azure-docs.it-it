---
title: Configurare un cluster Linux RDMA per eseguire applicazioni MPI | Microsoft Docs
description: Creare un cluster Linux con macchine virtuali di dimensioni H16r, H16mr, A8 o A9 per usare la rete RDMA di Azure per l'esecuzione di app MPI
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4b2ceb64b1737918458f6d5c692fc2bfbc0f12ed
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Configurazione di un cluster Linux RDMA per eseguire applicazioni MPI
Informazioni su come configurare un cluster Linux RDMA in Azure con [Dimensioni delle VM High Performance Computing (HPC)](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per eseguire applicazioni MPI (Message Passing Interface) in parallelo. Questo articolo illustra la procedura per preparare un'immagine Linux HPC per l'esecuzione di Intel MPI in un cluster. Dopo la preparazione viene distribuito un cluster di macchine virtuali usando questa immagine e una delle dimensioni di macchina virtuale di Azure con supporto per RDMA (attualmente H16r, H16mr, A8 o A9). Usare il cluster per eseguire applicazioni MPI in grado di comunicare in modo efficiente tramite una rete a bassa latenza e velocità effettiva elevata basata sulla tecnologia di accesso diretto a memoria remota (RDMA).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager](../../../resource-manager-deployment-model.md) e la distribuzione classica. Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## <a name="cluster-deployment-options"></a>Opzioni di distribuzione del cluster
Di seguito vengono riportati i metodi utilizzabili per creare un cluster Linux RDMA con o senza un'utilità di pianificazione del processo.

* **Script dell'interfaccia della riga di comando di Azure**: come illustrato più avanti in questo articolo, usare l'[interfaccia della riga di comando di Azure](../../../cli-install-nodejs.md) per eseguire lo script della distribuzione di un cluster di macchine virtuali con supporto per RDMA. L'interfaccia della riga di comando in modalità Service Management crea i nodi del cluster in modo seriale nel modello di distribuzione classica, quindi la distribuzione di molti nodi di calcolo potrebbe richiedere alcuni minuti. Per abilitare la connessione di rete RDMA quando si usa il modello di distribuzione classico, distribuire le macchine virtuali nello stesso servizio cloud.
* **Modelli di Azure Resource Manager**: è possibile anche usare il modello di distribuzione di Resource Manager per distribuire un cluster di macchine virtuali con supporto per RDMA che si connette alla rete RDMA. Per distribuire la soluzione desiderata, è possibile [creare un modello personalizzato](../../../resource-group-authoring-templates.md) o vedere la [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) per accedere ai modelli forniti da Microsoft o dalla community. I modelli di Gestione risorse riescono a fornire un modo veloce e affidabile per distribuire un cluster Linux. Per abilitare la connessione di rete RDMA quando si usa il modello di distribuzione di Resource Manager, distribuire le macchine virtuali nello stesso set di disponibilità.
* **HPC Pack**: creare un cluster Microsoft HPC Pack in Azure e aggiungere nodi di calcolo con supporto per RDMA che eseguono una distribuzione Linux supportata per accedere alla rete RDMA. Per ulteriori informazioni, vedere [Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Procedura per una distribuzione di esempio nel modello classico
I passaggi seguenti mostrano come usare l'interfaccia della riga di comando di Azure per distribuire una macchina virtuale HPC SUSE Linux Enterprise Server (SLES) 12 SP1 da Azure Marketplace, personalizzarla e creare un'immagine di macchina virtuale personalizzata. È quindi possibile usare l'immagine per lo script della distribuzione di un cluster di macchine virtuali con supporto per RDMA.

> [!TIP]
> Usare una procedura simile per distribuire un cluster di macchine virtuali con supporto per RDMA basate su immagini HPC basate su CentOS in Azure Marketplace. Come indicato, alcuni passaggi variano leggermente. 
>
>

### <a name="prerequisites"></a>Prerequisiti
* **Computer client**: è necessario un computer client Mac, Linux o Windows per comunicare con Azure. Nella procedura si presuppone che venga usato un client Linux.
* **Sottoscrizione di Azure**: se non è disponibile una sottoscrizione, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti. Per cluster di maggiori dimensioni, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto.
* **Disponibilità delle dimensioni di macchina virtuale**: offrono supporto per RDMA le dimensioni di istanza seguenti: H16r, H16mr, A8 e A9. Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/) .
* **Quota di core**: può essere necessario aumentare la quota di core per distribuire un cluster di macchine virtuali a elevato uso di calcolo. Ad esempio, se si desidera distribuire 8 VM A9 come illustrato in questo articolo, è necessario disporre di almeno 128 core. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di macchina virtuale, inclusa la serie H. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../../../azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito.
* **Interfaccia della riga di comando di Azure**: [installare](../../../cli-install-nodejs.md) l'interfaccia della riga di comando di Azure e [connetterla alla sottoscrizione di Azure](../../../xplat-cli-connect.md) dal computer client.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Provisioning di una macchina virtuale HPC SLES 12 SP1
Dopo l'accesso ad Azure con l'interfaccia della riga di comando di Azure, eseguire `azure config list` per verificare che l'output indichi la modalità Azure Service Management. In caso contrario, impostare la modalità eseguendo questo comando:

    azure config mode asm


Digitare il comando seguente per elencare tutte le sottoscrizioni per le quali si dispone dell'autorizzazione:

    azure account list

La sottoscrizione attualmente attiva sarà identificata da `Current` impostato su `true`. Se questa sottoscrizione non è quella che si desidera usare per creare il cluster, impostare l'ID sottoscrizione appropriato come sottoscrizione attiva:

    azure account set <subscription-Id>

Per visualizzare le immagini HPC SLES 12 SP1 disponibili pubblicamente in Azure, eseguire un comando simile al seguente, presupponendo che l'ambiente shell supporti **grep**:

    azure vm image list | grep "suse.*hpc"

Eseguire il provisioning di una macchina virtuale con supporto per RDMA con un'immagine HPC SLES 12 SP1 eseguendo un comando simile al seguente:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Dove:

* La dimensione (A9 in questo esempio) è una delle dimensioni di macchina virtuale con supporto per RDMA.
* Il numero della porta SSH esterna (22 in questo esempio, ovvero la porta SSH predefinita) può essere qualsiasi numero di porta valido. Il numero della porta SSH interna è impostato su 22.
* viene creato un nuovo servizio cloud nell'area di Azure specificata dalla località Specificare una posizione in cui sia disponibile la dimensione di macchina virtuale scelta.
* Attualmente il nome dell'immagine SLES 12 SP1 può essere una delle due opzioni seguenti per il supporto SUSE prioritario (sono previsti costi aggiuntivi). 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Personalizzazione della macchina virtuale
Quando la macchina virtuale completa il provisioning, assegnare una porta SSH alla macchina virtuale usando l'indirizzo IP esterno della macchina virtuale (o nome DNS) e il numero della porta esterna configurata, quindi personalizzarla. Per informazioni dettagliate sulla connessione, vedere [Come accedere a una macchina virtuale che esegue Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Eseguire i comandi come l'utente configurato nella VM, a meno che non sia necessario l'accesso alla radice per eseguire un passaggio.

> [!IMPORTANT]
> Microsoft Azure non fornisce l'accesso alla directory principale per le macchine virtuali Linux. Per ottenere l'accesso amministrativo quando si è connessi come utente alla macchina virtuale, eseguire i comandi con `sudo`.
>
>

* **Aggiornamenti**: installare gli aggiornamenti usando zypper. È anche possibile decidere di installare le utilità NFS.

  > [!IMPORTANT]
  > In una macchina virtuale HPC SLES 12 SP1 è consigliabile non applicare gli aggiornamenti del kernel, che possono causare problemi con i driver Linux RDMA.
  >
  >
* **Intel MPI**: completare l'installazione di Intel MPI nella macchina virtuale HPC SLES 12 SP1 eseguendo il comando seguente:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Blocco della memoria**: affinché i codici MPI blocchino la memoria disponibile per RDMA, aggiungere o modificare le impostazioni seguenti nel file /etc/security/limits.conf. Per modificare questo file è necessario l'accesso alla radice.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > A scopo di test, è inoltre possibile impostare memlock su illimitato. Ad esempio: `<User or group name>    hard    memlock unlimited`. Per altre informazioni, vedere [Metodi noti per l'impostazione delle dimensioni della memoria bloccata](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **Chiavi SSH per VM SLES**: generare chiavi SSH per stabilire una relazione di trust per il proprio account utente tra i nodi di calcolo del cluster SLES quando vengono eseguiti processi MPI. Se è stata distribuita una macchina virtuale HPC basata su CentOS, non seguire questo passaggio. Vedere le istruzioni più avanti nell'articolo per configurare un trust SSH senza password tra i nodi del cluster dopo aver acquisito l'immagine e distribuito il cluster.

    Usare il comando seguente per creare chiavi SSH. Quando viene richiesto, premere **INVIO** per generare le chiavi nel percorso predefinito senza impostare una password.

        ssh-keygen

    Aggiungere la chiave pubblica alla fine del file authorized_keys per le chiavi pubbliche note.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Nella directory ~/.ssh modificare o creare il file config. Specificare l'intervallo di indirizzi IP della rete privata che si prevede di usare in Azure (10.32.0.0/16 in questo esempio):

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

  > [!NOTE]
  > La configurazione di `StrictHostKeyChecking no` può creare un potenziale rischio di sicurezza quando non viene specificato un intervallo o un indirizzo IP specifico.
  >
  >
* **Applicazioni**: installare le applicazioni necessarie in questa macchina virtuale o eseguire altre personalizzazioni prima di acquisire l'immagine.

### <a name="capture-the-image"></a>Acquisire l'immagine
Per acquisire l'immagine, eseguire prima il comando seguente nella macchina virtuale Linux. Questo comando esegue il deprovisioning della VM, ma mantiene gli account utente e le chiavi SSH configurati.

```
sudo waagent -deprovision
```

Dal computer client, usare i seguenti comandi dell'interfaccia della riga di comando di Azure per acquisire l'immagine. Per ulteriori informazioni, vedere [Come acquisire una macchina virtuale Linux classica come immagine](capture-image.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Dopo l'esecuzione di questi comandi, l'immagine della macchina virtuale viene acquisita per l'uso e la macchina virtuale viene eliminata. Ora si dispone dell'immagine personalizzata pronta per distribuire un cluster.

### <a name="deploy-a-cluster-with-the-image"></a>Distribuzione di un cluster con l'immagine
Modificare lo script Bash seguente con i valori appropriati per il proprio ambiente ed eseguirlo dal computer client. Poiché Azure distribuisce le VM in modo seriale nel modello di distribuzione classica, sono necessari alcuni minuti per distribuire le otto macchine virtuali A9 suggerite in questo script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
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

## <a name="considerations-for-a-centos-hpc-cluster"></a>Considerazioni per un cluster HPC CentOS
Se si vuole impostare un cluster in base a una delle immagini HPC basate su CentOS in Azure Marketplace anziché SLES 12 per HPC, seguire la procedura generale riportata nella sezione precedente. Durante il provisioning e la configurazione della macchina virtuale, tenere presenti le differenze seguenti:

- Intel MPI è già installato in una macchina virtuale con provisioning da un'immagine HPC basata su CentOS.
- Le impostazioni di blocco della memoria sono già state aggiunte al file /etc/security/limits.conf della macchina virtuale.
- Non generare chiavi SSH sulla macchina virtuale di cui viene effettuato il provisioning per l'acquisizione. È invece consigliabile impostare l'autenticazione basata sull'utente dopo la distribuzione del cluster. Per altre informazioni, vedere la sezione seguente.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Impostare il trust SSH senza password nel cluster
In un cluster HPC basato su CentOS sono disponibili due metodi per stabilire relazioni di trust tra i nodi di calcolo: autenticazione basata sull'host e autenticazione basata sull'utente. L'autenticazione basata sull'host non rientra nell'ambito di questo articolo e in genere deve essere eseguita tramite uno script di estensione durante la distribuzione. L'autenticazione basata sull'utente è utile per stabilire un trust dopo la distribuzione e richiede la generazione e la condivisione di chiavi SSH tra i nodi di calcolo nel cluster. Questo metodo è comunemente noto come accesso SSH senza password ed è obbligatorio quando si eseguono processi MPI.

In [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) è disponibile uno script di esempio fornito dalla community per abilitare l'autenticazione utente semplice in un cluster HPC basato su CentOS. Scaricare e usare questo script con la procedura seguente. È anche possibile modificare lo script o usare qualsiasi altro metodo per stabilire l'autenticazione SSH senza password tra i nodi di calcolo del cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

Per eseguire lo script, è necessario conoscere il prefisso degli indirizzi IP della subnet. È possibile ottenere il prefisso con il comando seguente in uno dei nodi del cluster. L'output sarà essere simile a 10.1.3.5 e il prefisso è la parte 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Eseguire lo script con tre parametri: il nome utente comune nei nodi di calcolo, la password comune per tale utente nei nodi di calcolo e il prefisso della subnet restituito dal comando precedente.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Lo script esegue queste operazioni:

* Crea una directory nel nodo host denominata SSH, necessaria per l'accesso senza password.
* Crea un file di configurazione nella directory .ssh che indica di consentire l'accesso senza password da qualsiasi nodo del cluster.
* Crea file contenenti i nomi dei nodi e gli indirizzi IP dei nodi per tutti i nodi del cluster. Questi file vengono lasciati dopo l'esecuzione dello script come riferimento futuro.
* Crea una coppia di chiavi pubblica e privata per ogni nodo del cluster, incluso il nodo host, e crea una voce nel file authorized_keys.

> [!WARNING]
> L'esecuzione di questo script può creare un potenziale rischio per la sicurezza. Assicurarsi che le informazioni sulla chiave pubblica in ~/.ssh non vengano distribuite.
>
>

## <a name="configure-intel-mpi"></a>Configurare Intel MPI
Per eseguire applicazioni MPI su Azure Linux RDMA, è necessario configurare determinate variabili di ambiente specifiche di Intel MPI. Di seguito è riportato uno script Bash di esempio per la configurazione delle variabili necessarie per eseguire un'applicazione. Modificare il percorso in mpivars.sh secondo le esigenze per l'installazione di Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

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

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Eseguire MPI su un cluster di base a due nodi
Se non si è ancora provveduto, prima configurare l'ambiente per Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Eseguire un comando MPI
Eseguire un comando MPI su uno dei nodi di calcolo per vedere che MPI è installato correttamente e può comunicare tra almeno due nodi di calcolo. Il comando **mpirun** seguente esegue il comando **hostname** su due nodi.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
L'output dovrebbe elencare i nomi di tutti i nodi passati come input per `-hosts`. Ad esempio, un comando **mpirun** con due nodi restituisce un output simile al seguente:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Eseguire un benchmark MPI
Il comando Intel MPI seguente esegue un benchmark pingpong per verificare la configurazione del cluster e la connessione alla rete RDMA.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

In un cluster funzionante con due nodi dovrebbe essere visualizzato un output simile al seguente. Nella rete RDMA di Azure è prevista una latenza pari o inferiore a 3 microsecondi per i messaggi con una dimensione massima di 512 byte.

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



## <a name="next-steps"></a>Passaggi successivi
* Distribuire ed eseguire le applicazioni MPI Linux nel cluster Linux.
* Per istruzioni su Intel MPI, vedere la [documentazione relativa a Intel MPI Library](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/).
* Provare un [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) per creare un cluster Intel Lustre usando un'immagine basata HPC su CentOS. Per informazioni dettagliate, vedere [Distribuzione di Intel Cloud Edition per Lustre in Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).

