---
title: Eseguire NAMD con Microsoft HPC Pack sulle VM Linux | Microsoft Docs
description: "Informazioni su come distribuire un cluster Microsoft HPC Pack in Azure e come eseguire una simulazione NAMD con charmrun in più nodi di calcolo Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 0c0b9875b4153edcc0ec0096577d041d394a842f
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure
In questo articolo viene illustrato un modo per eseguire un carico di lavoro high-performance computing (HPC) Linux in macchine virtuali di Azure. Si configura un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) su Azure con nodi di calcolo Linux e si esegue una simulazione [NAMD](http://www.ks.uiuc.edu/Research/namd/) per calcolare e visualizzare la struttura di un sistema biomolecolare di grandi dimensioni.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (ovvero programma Nanoscale Molecular Dynamics) è un pacchetto di dinamica molecolare parallela progettato per una simulazione a prestazioni elevate di grandi sistemi biomolecolari contenenti fino a milioni di atomi. Esempi di questi sistemi includono virus, strutture di celle e proteine di grandi dimensioni. NAMD è scalabile fino a centinaia di core per simulazioni tipiche e fino a più di 500.000 core per le simulazioni più grandi.
* **Microsoft HPC Pack** fornisce le funzionalità necessarie per eseguire applicazioni HPC e parallele su larga scala in cluster di computer locali o macchine virtuali di Azure. Sviluppato originariamente come soluzione per i carichi di lavoro HPC di Windows, HPC Pack supporta ora le applicazioni HPC che eseguono Linux su macchine virtuali del nodo di calcolo Linux distribuite in un cluster HPC Pack. Per una panoramica, vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](hpcpack-cluster.md) .

## <a name="prerequisites"></a>Prerequisiti
* **Cluster HPC Pack con nodi di calcolo Linux**: distribuire un cluster HPC Pack con nodi di calcolo Linux in Azure usando un [modello di Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) o uno [script di Azure PowerShell](hpcpack-cluster-powershell-script.md). Per i prerequisiti e i passaggi di entrambe le opzioni, vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](hpcpack-cluster.md) . Se si sceglie l'opzione di distribuzione mediante uno script di PowerShell, vedere il file di configurazione di esempio al termine dell'articolo. Questo file consente di configurare un cluster HPC Pack basato su Azure costituito da un nodo head di Windows Server 2012 R2 e quattro nodi di calcolo CentOS 6.6 di grandi dimensioni. Personalizzare questo file in base all'ambiente in uso.
* **Software e file per le esercitazioni NAMD**: scaricare il software NAMD per Linux dal sito [NAMD](http://www.ks.uiuc.edu/Research/namd/). È richiesta la registrazione. Questo articolo è basato sulla versione NAMD 2.10 e usa l'archivio [Linux-x86_64 (64-bit Intel/AMD con Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310). Scaricare anche i [file per le esercitazioni NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). I download includono file con estensione .tar ed è necessario uno strumento di Windows per estrarre i file nel nodo head del cluster. Per estrarre i file, seguire le istruzioni indicate più avanti in questo articolo. 
* **VMD** (facoltativo) - Per visualizzare i risultati del processo NAMD, scaricare e installare il programma di visualizzazione molecolare [VMD](http://www.ks.uiuc.edu/Research/vmd/) nel computer desiderato. La versione corrente è 1.9.2. Per iniziare, vedere il sito per il download di VMD.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configurare il trust reciproco tra i nodi di calcolo
L'esecuzione di un processo su più nodi Linux richiede una relazione di trust tra i nodi (tramite **rsh** o **ssh**). Quando si crea il cluster HPC Pack con lo script di distribuzione IaaS di Microsoft HPC Pack, lo script configura automaticamente un trust reciproco permanente per l'account amministratore specificato. Per gli utenti non amministratori creati nel dominio del cluster è necessario configurare una relazione di trust reciproco temporanea tra i nodi in caso di allocazione di un processo. Quindi, eliminare la relazione dopo il completamento del processo. Per eseguire questa operazione per ogni utente, fornire una coppia di chiavi RSA al cluster usato da HPC Pack per stabilire la relazione di trust. Di seguito sono riportate le istruzioni.

### <a name="generate-an-rsa-key-pair"></a>Generare una coppia di chiavi RSA
Per generare con facilità una coppia di chiavi RSA, che contiene una chiave pubblica e una chiave privata, eseguire il comando **ssh-keygen** di Linux.

1. Accedere a un computer Linux.
2. Eseguire il comando seguente:
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Premere **Invio** per usare le impostazioni predefinite fino al completamento del comando. Non immettere una passphrase. Quando viene richiesta una password, è sufficiente premere **Invio**.
   > 
   > 
   
   ![Generare una coppia di chiavi RSA][keygen]
3. Passare alla directory ~/.ssh. La chiave privata viene archiviata in id_rsa e la chiave pubblica in id_rsa.pub.
   
   ![Chiavi pubbliche e private][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Aggiungere la coppia di chiavi al cluster HPC Pack
1. [Connettersi tramite Desktop remoto](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) alla VM con nodo head usando le credenziali del dominio specificate durante la distribuzione del cluster, ad esempio hpc\clusteradmin. Il cluster viene gestito dal nodo head.
2. Usare le procedure standard di Windows Server per creare un account utente di dominio nel dominio di Active Directory del cluster. Ad esempio, usare lo strumento Utenti e computer di Active Directory sul nodo head. Gli esempi in questo articolo presuppongono che venga creato un utente di dominio denominato hpclab\hpcuser nel dominio hpclab (hpclab\hpcuser).
3. Aggiungere l'utente di dominio al cluster HPC Pack come utente del cluster. Per le istruzioni, vedere [Aggiungere o rimuovere utenti del cluster](https://technet.microsoft.com/library/ff919330.aspx).
4. Creare un file con nome C:\cred.xml e copiarvi i dati della chiave RSA. Un esempio è disponibile nei file di esempio alla fine dell'articolo.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Aprire un prompt dei comandi e immettere il comando seguente per impostare i dati delle credenziali per l'account hpclab\hpcuser. Usare il parametro **extendeddata** per passare il nome del file C:\cred.xml creato per i dati della chiave.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Questo comando viene completato correttamente senza output. Dopo avere configurato le credenziali per gli account utente necessari per l'esecuzione dei progetti, archiviare il file cred.xml in un percorso sicuro oppure eliminarlo.
6. Se la coppia di chiavi RSA è stata generata in uno dei nodi Linux, ricordare di eliminare le chiavi dopo averle usate. HPC Pack non configura il trust reciproco se rileva un file id_rsa o id_rsa.pub esistente.

> [!IMPORTANT]
> Non è consigliabile eseguire un processo di Linux come amministratore del cluster in un cluster condiviso, perché un processo inviato da un amministratore viene eseguito con l'account radice nei nodi Linux. Un processo inviato da un utente non amministratore viene eseguito con un account utente Linux locale, con lo stesso nome dell'utente del processo. In questo caso, HPC Pack configura la relazione di trust reciproco per questo utente Linux in tutti i nodi allocati per il processo. È possibile configurare manualmente l'utente Linux nei nodi Linux prima di eseguire il processo. In alternativa, HPC Pack crea automaticamente l'utente quando viene inviato il processo. Se HPC Pack crea l'utente, HPC Pack lo eliminerà dopo il completamento del processo. Per ridurre le minacce alla sicurezza, le chiavi vengono rimosse dopo il completamento del processo sui nodi.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configurare una condivisione di file per i nodi Linux
Configurare ora una condivisione di file SMB e montare la cartella condivisa in tutti i nodi Linux per consentire ai nodi Linux di accedere ai file NAMD con un percorso comune. Di seguito vengono descritti i passaggi per montare una cartella condivisa nel nodo head. Per le distribuzioni che attualmente non supportano il servizio File di Azure, come ad esempio CentOS 6.6, si consiglia di eseguire una condivisione. Se i nodi Linux supportano la condivisione di file di Azure, vedere [Come usare Archiviazione file di Azure con Linux](../../../storage/files/storage-how-to-use-files-linux.md). Per altre opzioni di condivisione dei file con HPC Pack, vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](hpcpack-cluster.md).

1. Creare una cartella sul nodo head e condividerla con tutti gli utenti, configurando privilegi di lettura/scrittura. In questo esempio \\\\CentOS66HN\Namd è il nome della cartella, dove CentOS66HN è il nome host del nodo head.
2. Creare una sottocartella denominata namd2 nella cartella condivisa. All'interno di namd2, creare un'altra sottocartella namdsample.
3. Estrarre i file NAMD nella cartella usando una versione Windows di **tar** o un'altra utilità Windows che gestisce gli archivi con estensione tar. 
   
   * Estrarre l'archivio .tar NAMD in \\\\CentOS66HN\Namd\namd2.
   * Estrarre i file per le esercitazioni in \\\\CentOS66HN\Namd\namd2\namdsample.
4. Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per montare la cartella condivisa nei nodi Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Il primo comando crea una cartella denominata /namd2 su tutti i nodi del gruppo LinuxNodes. Il secondo comando monta la cartella condivisa //CentOS66HN/Namd/namd2 nella cartella con i bit dir_mode e file_mode impostati su 777. I valori di *username* e *password* nel comando devono corrispondere alle credenziali di un utente nel nodo head.

> [!NOTE]
> Il simbolo "\`" nel secondo comando è un simbolo di escape per PowerShell. "\`" significa "," (virgola) e fa parte del comando.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Creare uno script Bash per eseguire un processo NAMD
Il processo NAMD richiede un file *nodelist* per consentire a **charmrun** di determinare il numero di nodi da usare quando vengono avviati i processi NAMD. Viene usato uno script Bash che genera il file nodelist ed esegue **charmrun** con il file nodelist generato. È quindi possibile inviare un processo NAMD in HPC Cluster Manager per chiamare questo script.

Usando l'editor di testo desiderato, creare uno script Bash nella cartella /namd2 contenente i file di programma NAMD e denominarlo hpccharmrun.sh. Per un rapido modello di prova, copiare lo script di esempio hpccharmrun.sh fornito alla fine di questo articolo e passare a [Inviare un processo NAMD](#submit-a-namd-job).

> [!TIP]
> Salvare lo script come file di testo con terminazioni riga Linux (solo LF, non CR LF), in modo da assicurarne l'esecuzione corretta nei nodi Linux.
> 
> 

Di seguito sono descritte nel dettaglio le operazioni eseguite dallo script Bash. 

1. Definire alcune variabili.
   
   ```bash
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
2. Ottenere le informazioni sul nodo dalle variabili di ambiente. $NODESCORES archivia un elenco di parole suddivise da $CCP_NODES_CORES. $COUNT corrisponde alle dimensioni di $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   Il formato della variabile $CCP_NODES_CORES è il seguente:
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Questa variabile consente di elencare il numero totale di nodi, i nomi dei nodi e il numero di core in ogni nodo allocati al processo. Ad esempio, se per l'esecuzione del processo sono necessari 10 core, il valore di $CCP_NODES_CORES è simile a:
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Se la variabile $CCP_NODES_CORES non è configurata, avviare direttamente **charmrun**. È consigliabile seguire questa procedura solo quando si esegue lo script direttamente nei nodi Linux.
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. In alternativa, creare un file nodelist per **charmrun**.
   
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
5. Eseguire **charmrun** con il file nodelist, ottenere il relativo stato restituito e quindi rimuovere il file nodelist al termine delle operazioni.
   
   ${CCP_NUMCPUS} è un'altra variabile di ambiente configurata dal nodo head HPC Pack. Archivia il numero totale di core allocati per questo processo. Viene usata per specificare il numero di processi per charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Uscire con lo stato restituito di **charmrun** .
   
   ```
   exit ${RTNSTS}
   ```

Le informazioni seguenti sono disponibili nel file nodelist, che viene generato dallo script:

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

ad esempio:

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Inviare un processo NAMD
È ora possibile inviare un processo NAMD in HPC Cluster Manager.

1. Connettersi al nodo head del cluster e avviare HPC Cluster Manager.
2. In **Resource Management** (Gestione risorsa) assicurarsi che lo stato dei nodi di calcolo Linux sia **Online**. Se lo stato è diverso, selezionarli e fare clic su **Portare Online**.
3. In **Job Management** (Gestione processi) fare clic su **New Job** (Nuovo processo).
4. Immettere un nome per il processo, ad esempio *hpccharmrun*.
   
   ![Nuovo processo HPC][namd_job]
5. Nella pagina **Job Details** (Dettagli processo) in **Job Resources** (Risorse processo) scegliere **Node** (Nodo) come tipo di risorsa, quindi impostare il valore **Minimum** (Minimo) su 3 per eseguire il processo su tre nodi di Linux con quattro core ciascuno.
   
   ![Risorse del processo][job_resources]
6. Fare clic su **Edit Tasks** (Modifica attività) nel riquadro di spostamento sinistro e quindi fare clic su **Add** (Aggiungi) per aggiungere un'attività al processo.    
7. Nella pagina **Task Details and I/O Redirection** (Dettagli attività e reindirizzamento I/O) impostare i valori seguenti:
   
   * **Riga di comando**-
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > La riga di comando precedente è costituita da un comando singolo senza interruzioni di riga. Nella **riga di comando** il testo appare suddiviso in più righe.
     > 
     > 
   * **Working directory** - /namd2
   * **Minimum** - 3
     
     ![Dettagli dell'attività][task_details]
     
     > [!NOTE]
     > È necessario configurare qui la directory di lavoro, perché **charmrun** prova a passare alla stessa directory di lavoro in ogni nodo. Se la directory di lavoro non è configurata, HPC Pack avvia il comando in una cartella con nome casuale creata in uno dei nodi Linux. Ciò provoca l'errore seguente negli altri nodi: `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Per evitare questo problema, specificare un percorso di cartella accessibile a tutti i nodi come directory di lavoro.
     > 
     > 
8. Fare clic su **OK** e quindi su **Invia** per eseguire il processo.
   
   Per impostazione predefinita, HPC Pack invia il processo usando l'account utente attualmente connesso. È possibile che una finestra di dialogo richieda l'immissione del nome utente e della password dopo la selezione di **Submit**.
   
   ![Credenziali del processo][creds]
   
   In alcune condizioni, HPC Pack ricorda le informazioni utente inserite in precedenza e non visualizza questa finestra di dialogo. Per tornare a visualizzarla in HPC Pack, immettere il comando seguente nel prompt dei comandi e quindi inviare il processo.
   
   ```command
   hpccred delcreds
   ```
9. Il completamento del processo richiede alcuni minuti.
10. Il log del processo è disponibile in \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log e i file di output sono disponibili in \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\..
11. Facoltativamente, avviare VMD per visualizzare i risultati del processo. I passaggi per la visualizzazione dei file di output NAMD (in questo caso, una molecola proteica di ubiquitina in una sfera d'acqua) non rientrano nell'ambito di questo articolo. Per informazioni dettagliate, vedere l' [esercitazione relativa a NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) .
    
    ![Risultati del processo][vmd_view]

## <a name="sample-files"></a>File di esempio
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>File di configurazione XML di esempio per la distribuzione di cluster tramite script PowerShell
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

### <a name="sample-credxml-file"></a>File cred.xml di esempio
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

### <a name="sample-hpccharmrunsh-script"></a>Script hpccharmrun.sh di esempio
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

 

<!--Image references-->
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png

