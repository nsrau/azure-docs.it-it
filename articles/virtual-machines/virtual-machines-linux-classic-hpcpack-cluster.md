<properties
 pageTitle="Macchine virtuali di calcolo Linux in un cluster HPC Pack | Microsoft Azure"
 description="Informazioni su come creare e usare un cluster HPC Pack in Azure per carichi di lavoro HPC (High Performance Computing) Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="06/23/2016"
 ms.author="danlep"/>

# Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure

Configurare un cluster di Microsoft HPC Pack in Azure contenente un nodo head che esegue Windows Server e vari nodi di calcolo che eseguono una distribuzione Linux supportata. Sono disponibili varie opzioni per spostare dati tra i nodi Linux e il nodo head Windows del cluster. Leggere le informazioni su come inviare processi HPC Linux al cluster.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


Nel seguente diagramma viene illustrato il cluster HPC Pack che verrà creato e usato, a livello generale.

![Cluster HPC Pack con nodi Linux][scenario]


## Distribuzione di un cluster HPC Pack con nodi di calcolo Linux

I due modi consigliati per creare un cluster HPC Pack in Azure con nodi di calcolo Linux sono i seguenti:

* **Modello di Azure Resource Manager**: usare un modello di Azure Marketplace o un modello di avvio rapido fornito dalla community per automatizzare la creazione del cluster nel modello di distribuzione Resource Manager. Ad esempio, il modello [Cluster HPC Pack per carichi di lavoro Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) in Azure Marketplace consente di creare un'infrastruttura di cluster HPC Pack per carichi di lavoro HPC Linux.

* **Script di PowerShell**: usare lo [script di distribuzione Microsoft HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) (**New-HpcIaaSCluster.ps1**) per automatizzare la distribuzione di cluster completa nel modello di distribuzione classica. Questo script di Azure PowerShell usa un'immagine di macchina virtuale di HPC Pack in Azure Marketplace per accelerare la distribuzione e fornisce un set completo di parametri di configurazione per distribuire nodi di calcolo Linux.

Per una panoramica delle opzioni di distribuzione del cluster HPC Pack, vedere [Opzioni per creare e gestire un cluster di calcolo ad elevate prestazioni (HPC) in Azure con Microsoft HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).

### Prerequisiti

* **Sottoscrizione Azure**: è possibile utilizzare una sottoscrizione nel servizio Azure globale o Azure Cina. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* **Quota di core**: potrebbe essere necessario aumentare la quota di core, soprattutto se si sceglie di distribuire più nodi del cluster con dimensioni delle macchine virtuali multicore. Per aumentare una quota, aprire una richiesta di assistenza clienti online gratuitamente.

* **Distribuzioni Linux:** attualmente HPC Pack supporta le seguenti distribuzioni Linux per i nodi di calcolo. È possibile usare le versioni del Marketplace di queste distribuzioni, se disponibili, oppure fornire le proprie.

    * Basate su CentOS: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, HPC 6.5, HPC 7.1
    * Red Hat Enterprise Linux: 6.7, 6.8, 7.2
    * SUSE Linux Enterprise Server: SLES 12, SLES 12 (Premium), SLES 12 per HPC, SLES 12 per HPC (Premium)
    * Ubuntu Server: 14.04 LTS, 16.04 LTS



    >[AZURE.TIP]Per usare la rete RDMA di Azure con VM di nodi di calcolo di dimensioni A8 e A9, specificare una delle immagini HPC basate su HPC or CentOS di SUSE Linux Enterprise Server 12 da Marketplace. Per altre informazioni, vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](virtual-machines-linux-a8-a9-a10-a11-specs.md).

Prerequisiti aggiuntivi per distribuire il cluster usando lo script di distribuzione di HPC Pack IaaS:

* **Computer client**: sarà necessario un computer client basato su Windows per eseguire lo script di distribuzione del cluster.

* **Azure PowerShell**: [installare e configurare Azure PowerShell](../powershell-install-configure.md) (versione 0.8.10 o versione successiva) nel computer client.

* **Script di distribuzione di HPC Pack IaaS**: scaricare e decomprimere la versione più recente dello script dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). È possibile controllare la versione dello script eseguendo `.\New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione dello script 4.4.1 o successiva.

### Opzione di distribuzione 1. Usare il modello di Resource Manager

1. Andare al modello di [cluster HPC Pack per carichi di lavoro Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) in Azure Marketplace e fare clic su **Distribuzione**.

2. Nel portale di Azure verificare le informazioni e quindi fare clic su **Crea**.

    ![Creazione nel portale][portal]

3. Nel pannello **Informazioni di base** immettere un nome per il cluster, che sarà anche il nome della VM del nodo head. È possibile scegliere un gruppo di risorse esistente o creare un nuovo gruppo per la distribuzione.

4. Nel pannello **Impostazioni modo head**, per una prima distribuzione, in genere è possibile accettare le impostazioni predefinite.

    >[AZURE.NOTE]**URL script di post-configurazione** è un'impostazione facoltativa per specificare uno script di Windows PowerShell disponibile pubblicamente che si vuole eseguire nella VM del nodo head una volta che questa sia in esecuzione.
    
5. Nel pannello **Impostazioni nodo di calcolo** selezionare un pattern di nome per i nodi, il numero e le dimensioni dei nodi e la distribuzione Linux da distribuire.

6. Nel pannello **Impostazioni infrastruttura** immettere i nomi per la rete virtuale e il dominio di Active Directory per il cluster, le credenziali amministratore del dominio e della VM e un pattern di nome per gli account di archiviazione necessari per il cluster.

    >[AZURE.NOTE]HPC Pack usa il dominio di Active Directory per autenticare gli utenti del cluster.

7. Dopo l'esecuzione dei test di convalida e l'analisi delle condizioni per l'uso, fare clic su **Acquista**.


### Opzione di distribuzione 2. Uso dello script di distribuzione IaaS

Lo script di distribuzione di HPC Pack IaaS usa come input un file di configurazione XML che descrive l'infrastruttura del cluster HPC. Il file di configurazione di esempio seguente distribuisce un cluster di dimensioni ridotte costituito da un nodo head HPC Pack e due nodi di calcolo CentOS 7.0 Linux di dimensioni A7. Modificare il file in base alle necessità dell'ambiente e della configurazione cluster desiderata e salvarlo con un nome come HPCDemoConfig.xml. Ad esempio, è necessario specificare il nome della sottoscrizione e un nome di account di archiviazione univoco e un nome del servizio cloud ed è consigliabile scegliere una diversa immagine Linux supportata per i nodi di calcolo. Per altre informazioni sugli elementi del file di configurazione, vedere il file Manual.rtf nella cartella dello script e l'articolo [Creare un cluster HPC con lo script di distribuzione di HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

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
  <VMSize>ExtraLarge</VMSize>
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

**Per eseguire lo script di distribuzione di HPC Pack IaaS**

1. Aprire la console di PowerShell nel computer client come amministratore.

2. Passare alla cartella dello script (E:\\IaaSClusterScript in questo esempio).

    ```
    cd E:\IaaSClusterScript
    ```

3. Eseguire il comando seguente per distribuire il cluster HPC Pack. In questo esempio si presuppone che il file di configurazione si trovi in E:\\HPCDemoConfig.xml

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    Lo script genera automaticamente un file di log in quanto il parametro **-LogFile** non è specificato. I log non vengono scritti in tempo reale, ma vengono raccolti alla fine della convalida e della distribuzione. Se il processo di PowerShell viene arrestato mentre lo script è in esecuzione, alcuni log andranno persi.

    a. Poiché **AdminPassword** non è specificata nel comando precedente, verrà chiesto di immettere la password per l'utente *MyAdminName*.

    b. Lo script avvia quindi la convalida del file di configurazione. Questa operazione richiede alcuni minuti a seconda della connessione di rete.

    ![Convalida][validate]

    c. una volta superate le convalide, lo script elenca le risorse che verranno create per il cluster HPC. Immettere *Y* per continuare.

    ![Risorse][resources]

    d. Lo script inizia a distribuire il cluster HPC Pack e completa la configurazione senza ulteriori passaggi manuali. Ciò può richiedere alcuni minuti.

    ![Distribuire][deploy]

## Connettersi al nodo head

Al termine della distribuzione, [connettersi con Desktop remoto al nodo head](virtual-machines-windows-connect-logon.md) usando le credenziali del dominio specificate durante la distribuzione del cluster (ad esempio, *hpc\\clusteradmin*).

Nel nodo head avviare Gestione cluster HPC per controllare lo stato del cluster HPC Pack. È possibile gestire e monitorare i nodi di calcolo di Linux nello stesso modo dei nodi di calcolo di Windows. Ad esempio, verranno visualizzati i nodi Linux elencati in **Gestione risorse** (questi nodi vengono distribuiti con il modello **LinuxNode**).

![Gestione dei nodi][management]

I nodi Linux verranno visualizzati anche in **Mappa termica**.

![Mappa termica][heatmap]

## Spostamento dei dati in un cluster con nodi Linux

Sono disponibili varie opzioni per spostare dati tra i nodi Linux e il nodo head Windows del cluster. Di seguito sono riportati tre dei metodi più comuni.

* **File di Azure**: espone una condivisione file SMB gestita per archiviare i file di dati nella risorsa di archiviazione di Azure. I nodi di Windows e Linux possono montare contemporaneamente una condivisione di file Azure come un'unità o cartella anche se vengono distribuiti in reti virtuali differenti.

* **Condivisione SMB del nodo head**: monta una cartella condivisa Windows standard del nodo head in nodi Linux.

* **Server NFS del nodo head**: offre una soluzione di condivisione file per un ambiente misto Windows e Linux.

### Archiviazione file di Azure

Il servizio [File di Azure](https://azure.microsoft.com/services/storage/files/) espone condivisioni di file usando il protocollo SMB 2.1 standard. Le VM e i servizi cloud di Azure possono condividere i dati dei file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati dei file in una condivisione tramite l'API della risorsa di archiviazione File.

Per la procedura dettagliata per creare una condivisione file di Azure e per montarla nel nodo head, vedere [Introduzione all'archivio file di Azure in Windows](../storage/storage-dotnet-how-to-use-files.md). Per montare la condivisione file di Azure nei nodi Linux, vedere [Come usare l'archivio file di Azure con Linux](../storage/storage-how-to-use-files-linux.md). Per configurare connessioni persistenti, vedere la pagina relativa alle [connessioni persistenti a file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

Nell'esempio seguente creare una condivisione file di Azure in un account di archiviazione. Per il montaggio della condivisione nel nodo head, aprire un prompt dei comandi e immettere i comandi seguenti:

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

In questo esempio, allvhdsje è il nome dell'account di archiviazione, storageaccountkey è la chiave dell'account di archiviazione e rdma è il nome della condivisione file di Azure. La condivisione di File di Azure verrà montata su Z: nel nodo head.

Per il montaggio della condivisione di File di Azure in nodi Linux, eseguire un comando **clusrun** sul nodo head. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** è uno strumento di HPC Pack utile per eseguire attività amministrative su più nodi. Vedere anche [Clusrun per nodi Linux](#Clusrun-for-Linux-nodes) in questo articolo.

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

Il primo comando crea una cartella denominata /openfoam in tutti i nodi del gruppo LinuxNodes. Il secondo comando monta la cartella condivisa //CentOS7RDMA-HN/OpenFOAM nella cartella con dir e bit di modalità file impostati su 777. Il nome utente e la password nel comando devono corrispondere al nome utente e alla password di un utente del nodo head. Vedere [Aggiungere o rimuovere utenti del cluster](https://technet.microsoft.com/library/ff919330.aspx).

>[AZURE.NOTE]Il simbolo "`" nel secondo comando è un simbolo di escape per PowerShell. "`," significa che "," (virgola) è una parte del comando.


### Server NFS

Il servizio NFS consente di condividere e migrare i file tra computer che eseguono il sistema operativo Windows Server 2012 usando il protocollo SMB e i computer basati su Linux mediante il protocollo NFS. Il server NFS e tutti gli altri nodi devono essere distribuiti nella stessa rete virtuale. Consente di migliorare la compatibilità con i nodi Linux rispetto a una condivisione SMB. Ad esempio, supporta i collegamenti di file.

1. Per installare e configurare un server NFS, seguire la procedura riportata in [Server per condivisione primo Network File System end-to-end](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).

    Ad esempio, creare una condivisione NFS denominata nfs con le seguenti proprietà.

    ![Autorizzazione NFS][nfsauth]

    ![Autorizzazioni di condivisione NFS][nfsshare]

    ![Autorizzazioni NFS NTFS][nfsperm]

    ![Proprietà di gestione di NFS][nfsmanage]

2. Aprire una finestra di Windows PowerShell ed eseguire i comandi seguenti per montare la condivisione NFS.

  ```
  PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
  PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
  ```

  Il primo comando crea una cartella denominata /nfsshared su tutti i nodi del gruppo LinuxNodes. Il secondo comando consente di montare la condivisione CentOS7RDMA-HN:/nfs nella cartella. Qui CentOS7RDMA-HN:/nfs è il percorso remoto della condivisione NFS.

## Invio di processi
Esistono vari metodi per inviare i processi al cluster HPC Pack:

* Gestione cluster HPC o interfaccia grafica della Gestione cluster HPC

* Portale Web HPC

* API REST

La procedura di invio di processi al cluster in Azure tramite gli strumenti dell'interfaccia utente grafica di HPC Pack e il portale Web HPC è uguale a quella usata per i nodi di calcolo di Windows. Vedere [Gestione processi di HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) e [Come inviare processi da un computer client locale](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

Per inviare processi tramite l'API REST, vedere l'articolo relativo a [creazione e invio di processi tramite uso dell'API REST in Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Fare riferimento anche all'esempio Python nella pagina relativa all'[SDK HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) per inviare processi da un client Linux.

## ClusRun per i nodi Linux

Lo strumento **clusrun** di HPC Pack può essere usato per eseguire comandi su nodi Linux con un prompt dei comandi o Gestione cluster HPC. Di seguito sono riportati alcuni esempi di base.

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

>[AZURE.NOTE] Potrebbe essere necessario usare determinati caratteri di escape nei comandi **clusrun**. Come illustrato in questo esempio, usare ^ in un prompt dei comandi per eseguire l'escape del simbolo ">".

## Passaggi successivi

* Provare ad aumentare il numero di nodi del cluster o a eseguire un carico di lavoro Linux nel cluster. Per un esempio, vedere [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* Provare a usare un cluster con nodi di calcolo con dimensioni [A8 o A9](virtual-machines-windows-a8-a9-a10-a11-specs.md) per eseguire carichi di lavoro MPI. Per un esempio, vedere [Eseguire OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Se si è interessati a usare i nodi Linux in un cluster di HPC Pack locale, vedere le [linee guida di TechNet](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-classic-hpcpack-cluster/scenario.png
[portal]: ./media/virtual-machines-linux-classic-hpcpack-cluster/portal.png
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

<!---HONumber=AcomDC_0629_2016-->