<properties
 pageTitle="Macchine virtuali di calcolo Linux in un cluster HPC Pack | Microsoft Azure"
 description="Informazioni su come creare uno script di distribuzione di un cluster HPC Pack in Azure contenente un nodo head che esegue Windows Server con nodi di calcolo Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="12/02/2015"
 ms.author="danlep"/>

# Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure

Questo articolo illustra come usare uno script di Azure PowerShell per configurare un cluster di Microsoft HPC Pack in Azure contenente un nodo head che esegue Windows Server e vari nodi di calcolo che eseguono una distribuzione Linux. Vengono inoltre illustrati vari metodi per spostare i file di dati nei nodi di calcolo Linux. È possibile usare questo cluster per l'esecuzione di carichi di lavoro HPC Linux in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Nel seguente diagramma viene illustrato il cluster HPC Pack che verrà creato, a livello elevato.

![Cluster HPC con nodi Linux][scenario]

## Distribuzione di un cluster HPC Pack con nodi di calcolo Linux

Verrà usato lo script di distribuzione Microsoft HPC Pack IaaS (**New-HpcIaaSCluster.ps1**) per automatizzare la distribuzione di cluster nei servizi di infrastruttura di Azure (IaaS). In questo script di Azure PowerShell viene usata un'immagine di macchina virtuale di HPC Pack in Azure Marketplace per accelerare la distribuzione e viene fornito un set completo di parametri di configurazione per rendere la distribuzione semplice e flessibile. Lo script distribuisce rete virtuale di Azure, account di archiviazione, servizi cloud, controller di dominio, server di database SQL Server separato facoltativo, nodo head del cluster, nodi di calcolo, nodi broker, nodi di Azure PaaS ("burst") e nodi di calcolo Linux (supporto di Linux introdotto in [HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx)).

Per una panoramica delle opzioni di distribuzione del cluster HPC Pack, vedere [Guida introduttiva per HPC Pack 2012 R2 e HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx) e [Opzioni per creare e gestire un cluster di calcolo ad elevate prestazioni (HPC) in Azure con Microsoft HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).

### Prerequisiti

* **Computer client**: sarà necessario un computer client basato su Windows per eseguire lo script di distribuzione del cluster.

* **Azure PowerShell**: [installare e configurare Azure PowerShell](../powershell-install-configure.md) (versione 0.8.10 o versione successiva) nel computer client.

* **Script di distribuzione di HPC Pack IaaS**: scaricare e decomprimere la versione più recente dello script dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). È possibile controllare la versione dello script eseguendo `New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione dello script 4.4.0 o versione successiva.

* **Sottoscrizione di Azure**: è possibile usare una sottoscrizione nel servizio Azure globale o Azure Cina. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Quota di core**: potrebbe essere necessario aumentare la quota di core, soprattutto se si sceglie di distribuire più nodi del cluster con dimensioni delle macchine virtuali multicore. Ad esempio in questo articolo, devono essere disponibili almeno 12 core. Per aumentare una quota, è possibile [aprire una richiesta di assistenza clienti online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza alcun addebito.

### Creazione del file di configurazione

Lo script di distribuzione di HPC Pack IaaS usa come input un file di configurazione XML che descrive l'infrastruttura del cluster HPC. Per distribuire un cluster di dimensioni ridotte costituito da un nodo head e 2 nodi di calcolo Linux, sostituire i valori per il proprio ambiente nel file di configurazione di esempio riportato di seguito. Per altre informazioni sul file di configurazione, vedere il file Manual.rtf nella cartella dello script e l'articolo relativo alla [creazione di un cluster HPC con lo script di distribuzione di HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>A4</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

Di seguito sono riportate brevi descrizioni degli elementi del file di configurazione.

* **IaaSClusterConfig**: elemento radice del file di configurazione.

* **Subscription**: sottoscrizione di Azure usata per distribuire il cluster HPC Pack. Usare il comando riportato di seguito per verificare che il nome della sottoscrizione Azure sia configurato e univoco nel computer client. In questo esempio, viene usata la sottoscrizione di Azure "Sottoscrizione-1".

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]In alternativa, usare l'ID sottoscrizione per specificare la sottoscrizione che si vuole usare. Vedere il file Manual.rtf nella cartella dello script.

* **StorageAccount**: tutti i dati persistenti per il cluster HPC Pack verranno archiviati nell'account di archiviazione specificato (allvhdsje in questo esempio). Se l'account di archiviazione non esiste, lo script lo crea nell'area specificata in **Località**.

* **Località**: area di Azure in cui verrà distribuito il cluster HPC Pack (Giappone orientale in questo esempio).

* **VNet**: impostazioni della rete virtuale e subnet in cui verrà creato il cluster HPC. La rete virtuale e la subnet possono essere create manualmente prima di eseguire questo script, oppure lo script crea una rete virtuale con spazio di indirizzo 192.168.0.0/20 e una subnet con spazio di indirizzo 192.168.0.0/23. In questo esempio, lo script crea la rete virtuale centos7rdmavnetje e la subnet CentOS7RDMACluster.

* **Domain**: impostazioni del dominio di Active Directory per il cluster HPC Pack. Tutte le VM di Windows create dallo script vengono aggiunte al dominio. Attualmente, lo script supporta tre opzioni di dominio: ExistingDC, NewDC e HeadNodeAsDC. Questo esempio configura il nodo head come controller di dominio con nome di dominio completo hpc.local.

* **Database**: impostazioni database per il cluster HPC Pack. Attualmente, lo script supporta tre opzioni di database: ExistingDB, NewRemoteDB e LocalDB. Questo esempio crea un database locale nel nodo head.

* **HeadNode**: impostazioni per il nodo head di HPC Pack. Questo esempio crea un nodo head di dimensioni A7 denominato CentOS7RDMA-HN nel servizio cloud centos7rdma-je. Per supportare l'invio di processi HPC dai computer client remoti (non aggiunti al dominio), lo script abilita l'utilità di pianificazione di processi HPC API REST e un portale web HPC.

* **LinuxComputeNodes**: impostazioni per i nodi di calcolo HPC Pack Linux. Questo esempio crea due nodi di calcolo CentOS 7 Linux A7 (CentOS7RDMA-LN1 e CentOS7RDMA-LN2) nel servizio cloud centos7rdma-je.

### Considerazioni aggiuntive per i nodi di calcolo Linux

* HPC Pack supporta attualmente le seguenti distribuzioni Linux per nodi di calcolo: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 e SUSE Linux Enterprise Server 12.

* Nell'esempio riportato in questo articolo viene usata una versione specifica di CentOS disponibile in Azure Marketplace per creare il cluster. Per usare altre immagini disponibili, usare il cmdlet di Azure PowerShell **get-azurevmimage** per trovare l'immagine necessaria. Per elencare tutte le immagini CentOS 7.0, ad esempio, eseguire questo comando:

    ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Trovare l'immagine necessaria e sostituire il valore **ImageName** nel file di configurazione.

* Sono disponibili immagini Linux che supportano la connettività RDMA per VM di dimensioni A8 e A9. Se si specifica un'immagine con i driver RDMA Linux installati e attivati, lo script di distribuzione di HPC Pack IaaS li distribuisce. Specificare ad esempio il nome dell'immagine `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` per il SUSE Linux Enterprise Server 12 corrente: ottimizzato per l'immagine di calcolo ad alte prestazioni presente in Marketplace.

* Per abilitare Linux RDMA nelle VM Linux create dalle immagini supportate per l'esecuzione di processi MPI, installare e configurare una libreria specifica MPI nei nodi di Linux dopo la distribuzione del cluster in base alle esigenze dell'applicazione. Per un esempio, vedere [Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Distribuire tutti i nodi di Linux RDMA all'interno di un singolo servizio in modo che la connessione di rete RDMA possa funzionare tra i nodi.


## Esecuzione dello script di distribuzione di HPC Pack IaaS

1. Aprire la console di PowerShell nel computer client come amministratore.

2. Passare alla cartella dello script (E:\\IaaSClusterScript in questo esempio).

    ```
    cd E:\IaaSClusterScript
    ```

3. Eseguire il comando seguente per distribuire il cluster HPC Pack. In questo esempio si presuppone che il file di configurazione si trovi in E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    Lo script genera automaticamente un file di log in quanto il parametro **-LogFile** non è specificato. I log non vengono scritti in tempo reale, ma raccolti al termine della convalida e della distribuzione, pertanto se il processo di PowerShell viene arrestato mentre è in esecuzione lo script, alcuni log andranno persi.

    a. Poiché **AdminPassword** non è specificata nel comando precedente, verrà chiesto di immettere la password per l'utente *MyAdminName*.

    b. Lo script avvia quindi la convalida del file di configurazione. Questa operazione richiede da alcuni secondi a vari minuti a seconda della connessione di rete.

    ![Convalida][validate]

    c. una volta superate le convalide, lo script elenca le risorse che verranno create per il cluster HPC. Immettere *Y* per continuare.

    ![Risorse][resources]

    d. Lo script inizia a distribuire il cluster HPC Pack e completa la configurazione senza ulteriori passaggi manuali. Ciò può richiedere alcuni minuti.

    ![Distribuire][deploy]

4. Una volta completata correttamente la configurazione, usare Desktop remoto per il nodo head e aprire HPC Cluster Manager per controllare lo stato del cluster HPC Pack. È possibile gestire e monitorare i nodi di calcolo di Linux nello stesso modo dei nodi di calcolo di Windows. Ad esempio, i nodi di Linux saranno elencati nella gestione dei nodi.

    ![Gestione dei nodi][management]

    I nodi di Linux saranno inoltre visualizzati nella mappa termica.

    ![Mappa termica][heatmap]

## Spostamento dei dati in un cluster con nodi Linux

Sono disponibili varie opzioni per spostare dati tra i nodi Linux e il nodo head Windows del cluster. Di seguito sono riportati tre dei metodi più comuni.

* **File di Azure**: espone una condivisione file per archiviare i file di dati nella risorsa di archiviazione di Azure. I nodi di Windows e Linux possono montare contemporaneamente una condivisione di file Azure come un'unità o cartella anche se vengono distribuiti in reti virtuali differenti.

* **Condivisione SMB del nodo head**: monta una cartella condivisa del nodo head in nodi Linux.

* **Server NFS del nodo head**: offre una soluzione di condivisione file per un ambiente misto Windows e Linux.

### Archiviazione file di Azure

Il servizio [File di Azure](https://azure.microsoft.com/services/storage/files/) espone condivisioni di file usando il protocollo SMB 2.1 standard. Le VM e i servizi cloud di Azure possono condividere i dati dei file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati dei file in una condivisione tramite l'API della risorsa di archiviazione File. Per ulteriori informazioni, vedere [Come utilizzare l'archiviazione file di Azure con PowerShell e .NET](../storage/storage-dotnet-how-to-use-files.md).

Per creare una condivisione di File di Azure, vedere la procedura dettagliata nell'[introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Per configurare connessioni persistenti, vedere la pagina relativa alle [connessioni persistenti a file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

In questo esempio, verrà creata una condivisione di File di Azure denominata rdma nell'account di archiviazione allvhdsje. Per il montaggio della condivisione nel nodo head, aprire una finestra di comando e immettere i comandi seguenti:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

In questo esempio, allvhdsje è il nome dell'account di archiviazione, storageaccountkey è la chiave dell'account di archiviazione e rdma è il nome della condivisione di File di Azure. La condivisione di File di Azure verrà montata su Z: nel nodo head.

Per il montaggio della condivisione di File di Azure in nodi Linux, eseguire un comando **clusrun** sul nodo head. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** è uno strumento di HPC Pack utile per eseguire attività amministrative su più nodi. Vedere anche [CLusrun per nodi Linux](#CLusrun-for-Linux-nodes) in questo articolo.

Aprire una finestra di Windows PowerShell e immettere i seguenti comandi:

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Il primo comando crea una cartella denominata /rdma su tutti i nodi del gruppo LinuxNodes. Il secondo comando consente di montare la condivisione di File di Azure allvhdsjw.file.core.windows.net/rdma nella cartella /rdma con dir e bit di modalità file impostati su 777. Nel secondo comando, allvhdsje è il nome dell'account di archiviazione e storageaccountkey è la chiave dell'account di archiviazione.

>[AZURE.NOTE]Il simbolo "`" nel secondo comando è un simbolo di escape per PowerShell. "`," significa che "," (virgola) è una parte del comando.

### Condivisione del nodo head

In alternativa, montare una cartella condivisa del nodo head nei nodi Linux. Questo è il modo più semplice per condividere file, ma il nodo head e tutti i nodi Linux devono essere distribuiti nella stessa rete virtuale. Di seguito sono riportati i passaggi necessari.

1. Creare una cartella nel nodo head e condividerla per tutti gli utenti con autorizzazioni di lettura/scrittura. Ad esempio, condividere D:\\OpenFOAM nel nodo head come \\CentOS7RDMA-HN\\OpenFOAM. CentOS7RDMA-HN è il nome host del nodo head.

    ![Autorizzazioni di condivisione file][fileshareperms]

    ![Condivisione di file][filesharing]

2. Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per montare la cartella condivisa.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

Il primo comando crea una cartella denominata /openfoam in tutti i nodi del gruppo LinuxNodes. Il secondo comando monta la cartella condivisa //CentOS7RDMA-HN/OpenFOAM nella cartella con dir e bit di modalità file impostati su 777. Il nome utente e la password nel comando devono corrispondere al nome utente e alla password di un utente del nodo head.

>[AZURE.NOTE]Il simbolo "`" nel secondo comando è un simbolo di escape per PowerShell. "`," significa che "," (virgola) è una parte del comando.


### Server NFS

Il servizio NFS consente agli utenti di condividere e migrare i file tra computer che eseguono il sistema operativo Windows Server 2012 usando il protocollo SMB e i computer basati su Linux mediante il protocollo NFS. Il server NFS e tutti gli altri nodi devono essere distribuiti nella stessa rete virtuale. Consente di migliorare la compatibilità con i nodi di Linux rispetto a una condivisione SMB; ad esempio, supporta il collegamento di file.

1. Per installare e configurare un server NFS, seguire la procedura riportata in [Server per condivisione primo Network File System end-to-end](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).

    Ad esempio, creare una condivisione NFS denominata nfs con le seguenti proprietà.

    ![Autorizzazione NFS][nfsauth]

    ![Autorizzazioni di condivisione NFS][nfsshare]

    ![Autorizzazioni NFS NTFS][nfsperm]

    ![Proprietà di gestione di NFS][nfsmanage]

2. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per montare la condivisione NFS.

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  Il primo comando crea una cartella denominata /nfsshared su tutti i nodi del gruppo LinuxNodes. Il secondo comando consente di montare la condivisione CentOS7RDMA-HN:/nfs nella cartella. Qui CentOS7RDMA-HN:/nfs è il percorso remoto della condivisione NFS.

## Invio di processi
Esistono vari metodi per inviare i processi al cluster HPC Pack

* Gestione cluster HPC o interfaccia grafica della Gestione cluster HPC

* Portale Web HPC

* API REST

La procedura di invio di processi al cluster in Azure tramite gli strumenti dell'interfaccia utente grafica di HPC Pack e il portale Web HPC è uguale a quella usata per i nodi di calcolo di Windows. Vedere [Gestione processi di HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [Invio di processi da un client locale](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

Per inviare processi tramite l'API REST, vedere l'articolo relativo a [creazione e invio di processi tramite uso dell'API REST in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Fare riferimento anche all'esempio Python nella pagina relativa all'[SDK HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) per inviare processi da un client Linux.

## ClusRun per i nodi Linux

Lo strumento **clusrun** di HPC Pack può essere usato per eseguire comandi su nodi Linux tramite un prompt dei comandi o Gestione cluster HPC. Di seguito sono riportati alcuni esempi di base.

* Visualizzare i nomi utente correnti di tutti i nodi nel cluster.

    ```
    > clusrun whoami
    ```

* Installare lo strumento debugger **gdb** con **yum** in tutti i nodi del gruppo linuxnodes e riavviarli dopo 10 minuti.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* Creare uno script shell che visualizza tutti i numeri da 1 e 10 per un secondo in ogni nodo Linux del cluster, eseguirlo e visualizzare immediatamente l'output dei nodi.

    ```
    > clusrun /interleaved /nodegroup:linuxnodes echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE] Potrebbe essere necessario usare determinati caratteri di escape nei comandi **clusrun**. Come illustrato in questo esempio, usare ^ in una finestra di comando per eseguire l'escape del simbolo ">".

## Passaggi successivi

* Provare ad aumentare il numero di nodi del cluster o a eseguire un carico di lavoro Linux nel cluster. Per un esempio, vedere [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* Provare a usare un cluster con nodi di calcolo con dimensioni [A8 o A9](virtual-machines-windows-a8-a9-a10-a11-specs.md) per eseguire carichi di lavoro MPI. Per un esempio, vedere [Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Provare a usare un [modello della guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) con Gestione risorse di Azure per accelerare le distribuzioni di HPC Pack con un numero maggiore di nodi di calcolo Linux.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-classic-hpcpack-cluster/scenario.png
[validate]: ./media/virtual-machines-linux-classic-hpcpack-cluster/validate.png
[resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster/resources.png
[deploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster/deploy.png
[management]: ./media/virtual-machines-linux-classic-hpcpack-cluster/management.png
[heatmap]: ./media/virtual-machines-linux-classic-hpcpack-cluster/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-classic-hpcpack-cluster/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-classic-hpcpack-cluster/nfsmanage.png

<!---HONumber=AcomDC_0323_2016-->