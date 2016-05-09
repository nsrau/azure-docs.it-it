<properties
 pageTitle="Eseguire StarCCM+ con HPC Pack in macchine virtuali Linux | Microsoft Azure"
 description="Informazioni su come distribuire un cluster Microsoft HPC Pack in Azure ed eseguire un processo StarCCM+ in più nodi di calcolo Linux su una rete RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# Eseguire StarCCM+ con Microsoft HPC Pack in un cluster Linux RDMA in Azure
Questo articolo illustra come distribuire un cluster Microsoft HPC Pack in Azure ed eseguire un processo [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) su più nodi di calcolo Linux interconnessi con Infiniband.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack fornisce le funzionalità necessarie per eseguire svariate applicazioni HPC e parallele su larga scala, incluse le applicazioni MPI, in cluster di macchine virtuali di Microsoft Azure. HPC Pack supporta anche applicazioni HPC che eseguono Linux su macchine virtuali del nodo di calcolo Linux distribuite in un cluster HPC Pack. Per informazioni introduttive sull'uso dei nodi di calcolo Linux con HPC Pack, vedere [Introduzione ai nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Configurare il cluster HPC Pack
Scaricare gli script di distribuzione IaaS per HPC Pack da [questa pagina](https://www.microsoft.com/it-IT/download/details.aspx?id=44949) ed estrarli localmente.

Azure Powershell è un prerequisito. Se non è configurato nel computer locale, leggere l'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Al momento della stesura di questo documento, le immagini Linux dalla raccolta di Azure che include i driver Infiniband per Azure sono specifiche per SLES 12, CentOS 6.5 e CentOS 7.1. Questo articolo è basato sull'uso di SLES 12. Per recuperare il nome di tutte le immagini Linux che supportano HPC nella raccolta, è possibile eseguire il comando di PowerShell seguente:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

L'output elenca le posizioni in cui sono disponibili le immagini e il valore **ImageName** da usare in seguito nel modello di distribuzione. Prima di distribuire il cluster, è necessario compilare un file di modello di distribuzione per HPC Pack e, poiché si specifica come destinazione un cluster di piccole dimensioni, il nodo head sarà il controller di dominio e ospiterà un database SQL locale. Il modello seguente distribuirà un nodo head di questo tipo, creerà un nome file XML **MyCluster.xml** e sostituirà i valori di **SubscriptionId**, **StorageAccount**, **Location**, **VMName** e **ServiceName** con i valori dell'utente.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Avviare la creazione del nodo head eseguendo il comando di PowerShell in una finestra di comando con privilegi elevati:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Per la preparazione del nodo head sono necessari tra 20 e 30 minuti. È possibile connettersi al nodo dal portale di Azure facendo clic sull'icona Connetti della macchina virtuale.

Sarà infine necessario risolvere i problemi del server di inoltro DNS. A questo scopo, avviare il Gestore DNS.

1.  Fare clic con il pulsante destro del mouse sul nome del server in Gestore DNS, scegliere Proprietà e selezionare la scheda Server d'inoltro.

2.  Fare clic sul pulsante Modifica, rimuovere eventuali server d'inoltro, quindi fare clic su OK.

3.  Assicurarsi di selezionare la casella di controllo "Usa parametri radice se non sono disponibili server d'inoltro" e fare clic su OK.

## Configurare nodi di calcolo Linux
La distribuzione dei nodi di calcolo Linux viene eseguita mediante lo stesso modello di distribuzione usato in precedenza per la creazione del nodo head. Copiare il file **MyCluster.xml** dal computer locale al nodo head e aggiornare il tag **NodeCount** con il numero di nodi da distribuire (<=20). Assicurarsi di avere un numero sufficiente di core disponibili nella quota di Azure, perché ogni istanza A9 utilizzerà 16 core nella sottoscrizione. È possibile usare istanze A8 (8 core) invece di A9, se si vogliono usare più VM nello stesso budget.

Nel nodo head copiare gli script di distribuzione IaaS per HPC Pack.

Aprire un'istanza di Azure Powershell in una finestra di comandi con privilegi elevati:

1.  Eseguire **Add-AzureAccount** per connettersi alla sottoscrizione di Azure.

2.  Se sono disponibili più sottoscrizioni, eseguire **Get-AzureSubscription** per elencarle.

3.  Impostare una sottoscrizione predefinita eseguendo il comando **Select-AzureSubscription -SubscriptionName xxxx -Default**.

4.  Eseguire **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** per avviare la distribuzione dei nodi di calcolo Linux. ![Distribuzione del nodo head][hndeploy]

Aprire lo strumento HPC Pack Cluster Manager. Dopo alcuni minuti, i nodi di calcolo Linux verranno visualizzati normalmente in un elenco di nodi di calcolo del cluster. Con la modalità di distribuzione classica, le VM IaaS vengono create in modo sequenziale, quindi se il numero di nodi è importante, la distribuzione di tutte le VM potrebbe richiedere una quantità significativa di tempo. ![Nodi Linux in HPC Pack Cluster Manager][clustermanager]

Quando tutti i nodi sono attivi e in esecuzione sul cluster, è necessario configurare altri elementi dell'infrastruttura.

## Configurare una condivisione file di Azure per nodi Windows e Linux
Per semplificare, vengono usati i file di Azure, che offrono le funzionalità CIFS oltre ai BLOB di Azure come archivio permanente, per l'archiviazione di script, pacchetti dell'applicazione e file di dati. Si noti che questa non è la soluzione più ridimensionabile, ma si tratta della soluzione più semplice e non richiede macchine virtuali dedicate.

Creare una condivisione file di Azure seguendo le istruzioni disponibili nell'articolo [Introduzione ad Archiviazione file di Azure in Windows](..\storage\storage-dotnet-how-to-use-files.md)

Mantenere il nome dell'account di archiviazione **saname**, il nome della condivisione file **sharename** e la chiave dell'account di archiviazione **sakey**.

### Montaggio della condivisione file di Azure sul nodo head
Aprire un prompt dei comandi con privilegi elevati ed eseguire il comando seguente per archiviare le credenziali nell'insieme di credenziali del computer locale.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Per montare quindi la condivisione file di Azure eseguire:

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Montaggio della condivisione file di Azure sui nodi di calcolo Linux
Uno strumento utile disponibile in HPC Pack è l'utilità clusrun. Questa riga di comando consente di eseguire lo stesso comando simultaneamente su un set di nodi di calcolo. In questo caso viene usato per montare la condivisione file di Azure e renderla permanente, in modo da sopravvivere ai riavvii. In una finestra di comando con privilegi elevati sul nodo head eseguire i comandi seguenti.

Per creare la directory di montaggio:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Per montare la condivisione file di Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Per rendere persistente la condivisione di montaggio:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## Aggiornare i driver Linux
Potrebbe essere necessario aggiornare infine i driver Infiniband dei nodi di calcolo Linux. Per informazioni su se e come eseguire l'aggiornamento, vedere [Aggiornare i driver Linux RDMA per SLES 12](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12)

## Installare StarCCM+
Le istanze A8 e A9 delle macchine virtuali di Azure forniscono il supporto per Infiniband e le funzionalità RDMA. Al momento della stesura dell'articolo, i driver del kernel che abilitano queste funzionalità sono disponibili per immagini Windows 2012 R2 e SUSE 12 nella raccolta di Azure. I driver per CentOS 7.x saranno presto disponibili. Microsoft MPI e Intel MPI (versione 5.x) sono due librerie MPI che supportano questi driver in Azure.

CD-Adapco StarCCM+ 11.x e versioni successive è incluso in Intel MPI versione 5.x, quindi il supporto Infiniband per Azure è incluso.

Ottenere il pacchetto Linux64 StarCCM+ dal [portale di CD-Adapco](https://steve.cd-adapco.com). In questo caso è stata usata la versione 11.02.010 con precisione mista.

Nel nodo head nella condivisione file **/hpcdata** di Azure creare uno script della shell denominato **setupstarccm.sh** con i contenuti seguenti. Questo script verrà avviato in ogni nodo di calcolo per configurare StarCCM+ localmente.

#### Script setupstarcm.sh di esempio
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Per configurare StarCCM+ in tutti i nodi di calcolo Linux aprire una finestra di comandi con privilegi elevati ed eseguire il comando seguente:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Durante l'esecuzione del comando, è possibile monitorare l'utilizzo della CPU con la mappa termica di Cluster Manager. La configurazione di tutti i nodi dovrebbe richiedere qualche minuto.

## Esecuzione di processi StarCCM+
HPC Pack viene usato per le rispettive funzionalità per la pianificazione di processi in modo da eseguire processi StarCCM+. A questo scopo, è necessario il supporto di alcuni script usati per attivare il processo ed eseguire StarCCM+. I dati di input vengono mantenuti nella condivisione file di Azure prima di tutto per semplicità. Lo script di Powershell seguente consente di accodare un processo StarCCM+. Accetta tre argomenti:

*  Nome del modello
*  Numero di nodi da usare
*  Numero di core in ogni nodo da usare

Poiché StarCCM+ può utilizzare tutta la larghezza di banda della memoria, è in genere consigliabile usare un numero minore di core per ogni nodo di calcolo e aggiungere nuovi nodi. Il rapporto esatto tra core e nodi dipende dalla famiglia del processore e dalla velocità di interconnessione.

I nodi vengono allocati esclusivamente per il processo e non possono essere condivisi con altri processi. È possibile notare che il processo non viene avviato direttamente come processo MPI. Il servizio di avvio MPI verrà avviato dallo script della shell **runstarccm.sh**.

Il modello di input e **runstarccm.sh** devono essere archiviati nella condivisione **/hpcdata** montata in precedenza.

Ai file di log viene assegnato l'ID del processo come nome e i file vengono archiviati nella **condivisione /hpcdata**, insieme ai file di output di StarCCM+.


#### Script SubmitStarccmJob.ps1 di esempio
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Sostituire **runner.java** con il servizio di avvio preferito per il modello Java StarCCM+ e con il codice di registrazione.

#### Script runstarccm.sh di esempio
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
    	echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
    	let "I=${I}+2"
    	let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run the starccm with hostfile arg
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
    	-power -podkey "<yourkey>" -rsh ssh \
    	-mpi intel -fabric UDAPL -cpubind bandwidth,v \
    	-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
    	-batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Nel test viene usato un token di licenza di tipo Power-One-Demand per cui è necessario configurare la variabile di ambiente **$CDLMD\_LICENSE\_FILE** su ****1999@flex.cd-adapco.com** e la chiave nell'opzione**-podkey** della riga di comando.

Dopo alcune operazioni di inizializzazione, lo script estrae dalle variabili di ambiente **$CCP\_NODES\_CORES** configurate da HPC Pack l'elenco di nodi per la compilazione di un file host usato dal servizio di avvio MPI. Il file host conterrà l'elenco di nomi di nodi di calcolo usati per il processo, un nome per ogni riga.

Il formato della variabile **$CCP\_NODES\_CORES** segue questo modello:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

dove

* `<Number of nodes>`: è il numero di nodi allocato a questo processo.  
* `<Name of node_n_...>`: è il nome di ogni nodo allocato a questo processo.
* `<Cores of node_n_...>`: è il numero di core nel nodo allocato a questo processo.

Il numero di core **$NBCORES** viene calcolato anche in base al numero di nodi **$NBNODES** e al numero di core per nodo specificato come parametro **$NBCORESPERNODE**.

Ecco le opzioni MPI usate con Intel MPI su Azure:

*   -mpi intel => per specificare IntelMPI
*   -fabric UDAPL => per usare verbi Infiniband Azure
*   -cpubind bandwidth,v => per ottimizzare la larghezza di banda per MPI con StarCCM+
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => impostazioni di Intel MPI da usare con Infiniband Azure e configurare il numero necessario di core per ogni nodo
*   -batch => per avviare StarCCM+ in modalità batch senza interfaccia utente


Per avviare un processo, assicurarsi infine che i nodi siano attivi, in esecuzione e online in Cluster Manager. Da una finestra di comando di PowerShell eseguire quindi questo comando:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## Arresto dei nodi
Al termine dei test, per arrestare e avviare i nodi è possibile usare i comandi PowerShell seguenti per HPC Pack:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## Passaggi successivi
Provare a eseguire altri carichi di lavoro di Linux. Per esempi, vedere:

* [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* [Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->