<properties
 pageTitle="Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack | Microsoft Azure"
 description="Informazioni su come creare uno script di distribuzione di un cluster HPC Pack in Azure contenente un nodo head che esegue Windows Server con nodi di calcolo Linux."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/27/2015"
 ms.author="danlep"/>

# Introduzione all’uso di nodi di calcolo Linux in un cluster HPC Pack in Azure

In questo articolo viene illustrato come utilizzare uno script di Azure PowerShell per configurare un cluster di Microsoft HPC Pack in Azure contenente un nodo head che esegue Windows Server e vari nodi di calcolo che eseguono una distribuzione Linux CentOS. Vengono inoltre illustrati vari metodi per spostare i file di dati nei nodi di calcolo Linux. È possibile utilizzare questo cluster per l'esecuzione di carichi di lavoro HPC Linux in Azure.

Nel seguente diagramma viene illustrato il cluster HPC Pack che verrà creato, a livello elevato.

![Cluster HPC con nodi Linux][scenario]

## Distribuzione di un cluster HPC Pack con nodi di calcolo Linux

Verrà utilizzato lo script di distribuzione Microsoft HPC Pack IaaS (\*\* New-HpcIaaSCluster.ps1\*\*) per automatizzare la distribuzione di cluster in servizi di infrastruttura di Azure (IaaS). In questo script di Azure PowerShell viene utilizzata un'immagine di macchina virtuale di HPC Pack in Azure Marketplace per accelerare la distribuzione e viene fornito un set completo di parametri di configurazione per rendere la distribuzione semplice e flessibile. È possibile utilizzare lo script per distribuire: rete virtuale di Azure, account di archiviazione, servizi cloud, controller di dominio, server di database SQL Server separato facoltativo, nodo head del cluster, nodi di calcolo, nodi broker, nodi di Azure PaaS ("burst") e nodi di calcolo Linux (supporto di Linux introdotto in[HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx)).

Per una panoramica delle opzioni di distribuzione del cluster HPC Pack, vedere la [Guida introduttiva per HPC Pack 2012 R2 e HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx).

### Prerequisiti

* **Computer client**: sarà necessario un computer client basato su Windows per eseguire lo script di distribuzione del cluster.

* **Azure PowerShell**: [installare e configurare Azure PowerShell](../powershell-install-configure.md) (versione 0.8.10 o versione successiva) nel computer client.

* **Script di distribuzione di HPC Pack IaaS**: scaricare e decomprimere la versione più recente dello script dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). È possibile controllare la versione dello script eseguendo `New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione dello script 4.4.0 o versione successiva.

* **Sottoscrizione Azure**: è possibile utilizzare una sottoscrizione nel servizio Azure globale o Azure Cina. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Quota di core**: potrebbe essere necessario aumentare la quota di core, soprattutto se si sceglie di distribuire più nodi del cluster con dimensioni delle macchine virtuali multicore. Ad esempio in questo articolo, sono necessari almeno 24 core. Per aumentare una quota, [aprire una richiesta di assistenza clienti online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitamente.

### Creazione del file di configurazione
Lo script di distribuzione di HPC Pack IaaS utilizza come input un file di configurazione XML che descrive l'infrastruttura del cluster HPC. Per distribuire un cluster di dimensioni ridotte costituito da un nodo head e 2 nodi di calcolo Linux, sostituire i valori per il proprio ambiente nel file di configurazione di esempio riportato di seguito. Per ulteriori informazioni sul file di configurazione, vedere il file Manual.rtf nella cartella dello script o la [documentazione dello script](https://msdn.microsoft.com/library/azure/dn864734.aspx).

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

* **Sottoscrizione**: sottoscrizione di Azure utilizzata per distribuire il cluster HPC Pack. Utilizzare il comando riportato di seguito per verificare che il nome della sottoscrizione Azure sia configurato e univoco nel computer client. In questo esempio, viene utilizzata la sottoscrizione di Azure "Sottoscrizione-1".

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]In alternativa, è possibile utilizzare l'ID sottoscrizione per specificare la sottoscrizione che si desidera utilizzare. Vedere il file Manual.rtf nella cartella dello script.

* **StorageAccount**: tutti i dati persistenti per il cluster HPC Pack verranno archiviati nell'account di archiviazione specificato (allvhdsje in questo esempio). Se l'account di archiviazione non esiste, lo script lo crea nell'area specificata in **Località**.

* **Località**: area di Azure in cui verrà distribuito il cluster HPC Pack (Giappone orientale in questo esempio).

* **VNet**: impostazioni della rete virtuale e subnet in cui verrà creato il cluster HPC. La rete virtuale e la subnet possono essere create manualmente prima di eseguire questo script, oppure lo script crea una rete virtuale con spazio di indirizzo 192.168.0.0/20 e una subnet con spazio di indirizzo 192.168.0.0/23. In questo esempio, lo script crea la rete virtuale centos7rdmavnetje e la subnet CentOS7RDMACluster.

* **Dominio**: impostazioni del dominio Active Directory per il cluster HPC Pack. Tutte le VM di Windows create dallo script vengono aggiunte al dominio. Attualmente, lo script supporta tre opzioni di dominio: ExistingDC, NewDC e HeadNodeAsDC. In questo esempio, viene configurato il nodo head come controller di dominio. Il nome di dominio completo è hpc.local.

* **Database**: impostazioni database per il cluster HPC Pack. Attualmente, lo script supporta tre opzioni di database: ExistingDB, NewRemoteDB e LocalDB. In questo esempio, verrà creato un database locale nel nodo head.

* **HeadNode**: impostazioni per il nodo head di HPC Pack. In questo esempio, verrà creato un nodo head di dimensioni A7 denominato CentOS7RDMA-HN nel servizio cloud centos7rdma-je. Per supportare l'invio di processi HPC dai computer client remoti (non aggiunto al dominio), lo script abilita l'utilità di pianificazione di processi HPC API REST e un portale web HPC.

* **LinuxComputeNodes**: impostazioni per i nodi di calcolo HPC Pack Linux. In questo esempio, verranno creati due nodi di calcolo CentOS 7 Linux A7 (CentOS7RDMA-LN1 e CentOS7RDMA-LN2) nel servizio cloud centos7rdma-je.

### Considerazioni aggiuntive per i nodi di calcolo Linux

* HPC Pack supporta attualmente le seguenti distribuzioni Linux per nodi di calcolo: Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 e SUSE Linux Enterprise Server 12.

* Nell'esempio riportato in questo articolo viene utilizzata una versione specifica di CentOS disponibile in Azure Marketplace per creare il cluster. Se si desidera utilizzare altre immagini disponibili, utilizzare il cmdlet di Azure PowerShell **get-azurevmimage** per trovare l’immagine necessaria Per elencare tutte le immagini CentOS 7.0, ad esempio, eseguire il comando seguente:```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Individuare l’immagine necessaria e sostituire il valore **ImageName** nel file di configurazione.

* Sono disponibili immagini Linux che supportano la connettività RDMA per VM di dimensioni A8 e A9. Se si specifica un'immagine con i driver RDMA Linux installati e attivati, lo script di distribuzione di HPC Pack IaaS li distribuisce. Ad esempio, è possibile specificare il nome dell'immagine `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` per il SUSE Linux Enterprise Server 12 corrente: ottimizzato per l’immagine di calcolo ad alte prestazioni presente in Marketplace.


* Per abilitare Linux RDMA nelle VM Linux create dalle immagini supportate per l'esecuzione di processi MPI, installare e configurare una libreria specifica MPI nei nodi di Linux dopo la distribuzione del cluster in base alle esigenze dell’applicazione. Per ulteriori informazioni su come utilizzare RDMA nei nodi Linux in Azure, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md).

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

    c. una volta superate le convalide, lo script elenca le risorse che verranno create per il cluster HPC. Immettere *y* per continuare.

    ![Risorse][resources]

    d. Quindi, lo script inizia a distribuire il cluster HPC Pack e completerà la configurazione senza ulteriori passaggi manuali. Ciò può richiedere alcuni minuti.

    ![Distribuire][deploy]

4. Una volta completata correttamente la configurazione, utilizzare Desktop remoto per il nodo head e aprire HPC Cluster Manager per controllare lo stato del cluster HPC Pack. È possibile gestire e monitorare i nodi di calcolo di Linux nello stesso modo dei nodi di calcolo di Windows. Ad esempio, i nodi di Linux saranno elencati nella gestione dei nodi.

    ![Gestione dei nodi][management]

    I nodi di Linux saranno inoltre visualizzati nella mappa termica.

    ![Mappa termica][heatmap]

## Spostamento dei dati in un cluster con nodi Linux

Sono disponibili varie opzioni per spostare dati tra i nodi Linux e il nodo head Windows del cluster. Di seguito sono riportati tre dei metodi più comuni.

* **File di Azure**: espone una condivisione file per archiviare i file di dati nella risorsa di archiviazione di Azure. I nodi di Windows e Linux possono montare contemporaneamente una condivisione di file Azure come un'unità o cartella anche se vengono distribuiti in reti virtuali differenti.

* **Condivisione SMB del nodo head**: consente di montare una cartella condivisa del nodo head in nodi di Linux.

* **Server NFS del nodo head**: offre una soluzione di condivisione file per un ambiente misto Windows e Linux.

### File di Azure

Il servizio [File di Azure](https://azure.microsoft.com/services/storage/files/) espone condivisioni di file utilizzando il protocollo SMB 2.1 standard. Le VM e i servizi cloud di Azure possono condividere i dati dei file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati dei file in una condivisione tramite l'API della risorsa di archiviazione File. Per ulteriori informazioni, vedere [Come utilizzare l'archiviazione file di Azure con PowerShell e .NET](../storage/storage-dotnet-how-to-use-files.md).

Per creare una condivisione di File di Azure, vedere la procedura dettagliata in [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Per configurare connessioni persistenti, vedere [Connessioni persistenti a file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

In questo esempio, verrà creata una condivisione di File di Azure denominata rdma nell’account di archiviazione allvhdsje. Per il mount della condivisione nel nodo head, aprire una finestra di comando e immettere i comandi seguenti:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

In questo esempio, allvhdsje è il nome dell’account di archiviazione, storageaccountkey è la chiave dell’account di archiviazione e rdma è il nome della condivisione di File di Azure. La condivisione di File di Azure verrà montata su Z: nel nodo head.

Per il mount della condivisione di File di Azure su nodi di Linux, eseguire un comando **clusrun** sul nodo head. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**è uno strumento di HPC Pack utile per eseguire attività amministrative su più nodi. (Vedere anche [CLusrun per nodi Linux](#CLusrun-for-Linux-nodes) in questo articolo).

Aprire una finestra di Windows PowerShell e immettere i seguenti comandi:

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Il primo comando crea una cartella denominata /rdma su tutti i nodi del gruppo LinuxNodes. Il secondo comando consente di montare la condivisione di File di Azure allvhdsjw.file.core.windows.net/rdma nella cartella /rdma con dir e bit di modalità file impostati su 777. Nel secondo comando, allvhdsje è il nome dell’account di archiviazione e storageaccountkey è la chiave dell’account di archiviazione.

>[AZURE.NOTE]Il simbolo “`” nel secondo comando è un simbolo di escape per PowerShell. “`,” significa che ","(una virgola) è una parte del comando.

### Condivisione del nodo head

In alternativa è possibile montare una cartella condivisa del nodo head sui nodi Linux. Questo è il modo più semplice per condividere file, ma il nodo head e tutti i nodi Linux devono essere distribuiti nella stessa rete virtuale. Di seguito sono riportati i passaggi necessari.

1. Creare una cartella nel nodo head e condividerla per tutti gli utenti con autorizzazioni di lettura/scrittura. Ad esempio, D:\\OpenFOAM viene condivisa nel nodo head come \\CentOS7RDMA-HN\\OpenFOAM. CentOS7RDMA-HN è il nome host del nodo head.

    ![Autorizzazioni di condivisione file][fileshareperms]

    ![Condivisione di file][filesharing]

2. Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per montare la cartella condivisa.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>,password='<password>’,dir_mode=0777`,file_mode=0777
```

Il primo comando crea una cartella denominata /openfoam su tutti i nodi del gruppo LinuxNodes. Il secondo comando monta la cartella condivisa //CentOS7RDMA-HN/OpenFOAM nella cartella con dir e bit di modalità file impostati su 777. Nome utente e password nel comando devono essere il nome utente e la password di un utente del nodo head.

>[AZURE.NOTE]Il simbolo “`” nel secondo comando è un simbolo di escape per PowerShell. “`,” significa che ","(una virgola) è una parte del comando.


### Server NFS

Il servizio NFS consente agli utenti di condividere e migrare i file tra computer che eseguono il sistema operativo Windows Server 2012 utilizzando il protocollo SMB e i computer basati su Linux mediante il protocollo NFS. Il server NFS e tutti gli altri nodi devono essere distribuiti nella stessa rete virtuale. Consente di migliorare la compatibilità con i nodi di Linux rispetto a una condivisione SMB; ad esempio, supporta il collegamento di file.

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

La procedura di invio di processi al cluster in Azure tramite gli strumenti dell’interfaccia utente grafica di HPC Pack e il portale Web HPC è uguale a quella utilizzata per i nodi di calcolo di Windows. Vedere [Gestione processi di HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [Invio di processi da un client locale](https://msdn.microsoft.com/library/azure/dn689084.aspx).

Per inviare processi tramite l'API REST, fare riferimento a [Creazione e invio di processi tramite uso dell’API REST in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Fare inoltre riferimento all'esempio Python in [SDK HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) per inviare processi da un client Linux.

## ClusRun per i nodi Linux

Lo strumento **clusrun** di HPC Pack può essere utilizzato per eseguire comandi su nodi Linux tramite una finestra di comando o Gestione cluster HPC. Di seguito sono riportati alcuni esempi.

* Visualizzare i nomi utente correnti di tutti i nodi nel cluster

    ```
> clusrun whoami
```

* Installare lo strumento debugger **gdb** ith **yum** su tutti i nodi del gruppo linuxnodes e riavviarli dopo 10 minuti

    ```
> clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
```

* Creare uno script della shell che visualizzi da 1 a 10 al secondo sui nodi cluster, eseguirlo e visualizzare immediatamente gli output di ogni nodo.

    ```
> clusrun /interleaved echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh```

>[AZURE.NOTE]Potrebbe essere necessario utilizzare determinati caratteri di escape nei comandi clusrun. Utilizzare ^ in una finestra di comando e ' in PowerShell per trasformare i caratteri speciali. Ad esempio, in PowerShell, i caratteri virgola e punto e virgola devono essere trasformati da ', e ', rispettivamente. Questi caratteri non richiedono la trasformazione in una finestra di comando.




## Passaggi successivi

* Utilizzare **clusrun** per installare l’applicazione Linux sui nodi di calcolo Linux e inviare un processo al cluster di HPC Pack.

* Provare a scalare il cluster in un maggior numero di nodi oppure a distribuire nodi di calcolo di dimensione [A8 o A9](virtual-machines-a8-a9-a10-a11-specs.md) per eseguire carichi di lavoro MPI.


<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=July15_HO5-->