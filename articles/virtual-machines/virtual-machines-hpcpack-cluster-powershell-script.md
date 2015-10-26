<properties
   pageTitle="Script PowerShell per distribuire cluster HPC Pack | Microsoft Azure"
   description="Eseguire lo script di Windows PowerShell per distribuire un cluster HPC Pack completo in servizi di infrastruttura di Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="big-compute"
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Creare un cluster HPC (High Performance Computing) nelle macchine virtuali di Azure con lo script di distribuzione IaaS di HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.



Eseguire lo script di PowerShell per la distribuzione IaaS di HPC Pack in un computer client per distribuire un cluster HPC Pack completo in servizi di infrastruttura di Azure (IaaS). Lo script offre numerose opzioni di distribuzione e consente di aggiungere nodi di calcolo cluster che eseguono distribuzioni di Linux supportate o sistemi operativi Windows Server.

A seconda dell'ambiente e delle scelte effettuate, lo script può creare tutta l'infrastruttura del cluster, inclusi la rete virtuale di Azure, gli account di archiviazione, i servizi cloud, il controller di dominio, i database SQL locali o remoti, il nodo head, i nodi broker, i nodi di calcolo e i nodi del servizio cloud ("burst" o PaaS) di Azure. In alternativa, lo script può usare l'infrastruttura di Azure preesistente e quindi creare il nodo head, i nodi broker, i nodi di calcolo e i nodi burst di Azure del cluster HPC.


Per informazioni generali sulla pianificazione di un cluster HPC Pack, vedere gli articoli relativi a [valutazione e pianificazione del prodotto](https://technet.microsoft.com/library/jj899596.aspx) e all'[introduzione](https://technet.microsoft.com/library/jj899590.aspx) nella Libreria TechNet per HPC Pack.

>[AZURE.NOTE]Per distribuire un cluster HPC Pack è anche possibile usare un modello di Gestione risorse di Azure. Per un esempio, vedere [Creare un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), [Creare un cluster HPC con un'immagine di nodo di calcolo personalizzata](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/), o [Creare un cluster HPC con nodi di calcolo Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

## Prerequisiti

* **Sottoscrizione Azure**: è possibile utilizzare una sottoscrizione nel servizio Azure globale o Azure Cina. I limiti della sottoscrizione riguardano il numero e il tipo di nodi del cluster che è possibile distribuire. Per informazioni, vedere [Limiti, quote e vincoli delle sottoscrizioni e dei servizi di Microsoft Azure](../azure-subscription-service-limits.md).


* **Computer client Windows in cui è stato installato e configurato Azure PowerShell 0.8.7 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Lo script viene eseguito in Gestione del servizio Azure.


* **Script di distribuzione di HPC Pack IaaS**: scaricare e decomprimere la versione più recente dello script dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). È possibile controllare la versione dello script eseguendo `New-HPCIaaSCluster.ps1 –Version`. Questo articolo si basa sulla versione dello script 4.4.0.

* **File di configurazione script**: sarà necessario creare un file XML usato dallo script per configurare il cluster HPC. Per informazioni ed esempi, vedere le sezioni più avanti in questo argomento.


## Sintassi

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]È necessario eseguire lo script come amministratore.

### Parametri

* **ConfigFile**: specifica il percorso del file di configurazione che descrive il cluster HPC. Per altre informazioni sul file di configurazione, vedere [File di configurazione](#Configuration-file) in questo argomento o il file Manual.rtf, disponibile nella cartella che contiene lo script.

* **AdminUserName**: specifica il nome utente. Se la foresta di domini viene creata dallo script, si tratta del nome utente amministratore locale per tutte le macchine virtuali, nonché del nome dell'amministratore di dominio. Se la foresta di domini esiste già, specifica l'utente di dominio come nome utente amministratore locale per installare HPC Pack.

* **AdminPassword**: specifica la password dell'amministratore. Se non è specificato nella riga di comando, lo script richiederà di immettere la password.

* **HPCImageName** (facoltativo): specifica il nome immagine di macchina virtuale HPC Pack usato per distribuire il cluster HPC. Deve essere un'immagine HPC Pack fornita da Microsoft da Azure Marketplace. Se non è specificato (scelta consigliata nella maggior parte dei casi), lo script sceglie l'ultima immagine HPC Pack pubblicata.

    >[AZURE.NOTE]Se non si specifica un'immagine HPC Pack valida, la distribuzione non riuscirà.

* **LogFile** (facoltativo): specifica il percorso del file di log della distribuzione. Se non è specificato, lo script creerà un file di log nella directory temporanea del computer che esegue lo script.

* **Force** (facoltativo): elimina tutte le richieste di conferma.

* **NoCleanOnFailure** (facoltativo): specifica che le macchine virtuali di Azure distribuite non correttamente non verranno rimosse. È necessario rimuovere manualmente queste macchine virtuali prima di eseguire nuovamente lo script per continuare la distribuzione. In caso contrario, la distribuzione potrebbe non riuscire.

* **PSSessionSkipCACheck** (facoltativo): per ogni servizio cloud con macchine virtuali distribuite da questo script, viene automaticamente generato da Azure un certificato autofirmato, che viene usato da tutte le macchine virtuali nel servizio cloud come certificato di Gestione remota Windows (WinRM) predefinito. Per distribuire funzionalità HPC in queste macchine virtuali di Azure, per impostazione predefinita lo script installa temporaneamente questi certificati nell'archivio Computer locale\\Autorità di certificazione radice attendibili del computer client, in modo da eliminare l'errore di sicurezza "CA non attendibile" durante l'esecuzione dello script. I certificati vengono rimossi al termine dello script. Se questo parametro è specificato, i certificati non vengono installati nel computer client e l'avviso di sicurezza viene eliminato.

    >[AZURE.IMPORTANT]Questo parametro non è consigliato per distribuzioni di produzione.

### Esempio

L'esempio seguente crea un nuovo cluster HPC Pack usando il file di configurazione MyConfigFile.xml e specifica le credenziali amministrative per l'installazione del cluster. ```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName
<username> –AdminPassword <password>
```
### Considerazione aggiuntive

* Lo script usa l'immagine di macchina virtuale HPC Pack in Azure Marketplace per creare il nodo head del cluster. L'immagine corrente è basata su Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 2 installato.

* Lo script può facoltativamente consentire l'invio di processi tramite il portale Web di HPC Pack o l'API REST HPC Pack.


* Se si vuole installare altro software o configurare altre impostazioni, lo script può facoltativamente eseguire script di preconfigurazione e post-configurazione personalizzati nel nodo head.


## File di configurazione

Il file di configurazione per lo script di distribuzione è un file XML. Il file di schema HPCIaaSClusterConfig.xsd si trova nella cartella dello script di distribuzione IaaS di HPC Pack. **IaaSClusterConfig** è l'elemento radice del file di configurazione, che contiene gli elementi figlio descritti in dettaglio nel file Manual.rtf nella cartella dello script di distribuzione. Per i file di esempio per scenari diversi, vedere [File di configurazione di esempio](#Example-configuration-files) in questo articolo.

## File di configurazione di esempio

### Esempio 1

Il file di configurazione seguente distribuisce un cluster HPC Pack in una foresta di domini esistente. Il cluster include 1 nodo head con database locali e 12 nodi di calcolo con l'estensione BGInfo applicata. L'installazione automatica degli aggiornamenti di Windows è disabilitata per tutte le macchine virtuali nella foresta di domini. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. I nodi di calcolo vengono creati in 3 servizi cloud e 3 account di archiviazione (ad esempio, da MyHPCCN-0001 a MyHPCCN-0005 in MyHPCCNService01 e mycnstorage01, da MyHPCCN-0006 a MyHPCCN0010 in MyHPCCNService02 e mycnstorage02 e da MyHPCCN-0011 a MyHPCCN-0012 in MyHPCCNService03 e mycnstorage03). I nodi di calcolo vengono creati da un'immagine privata esistente acquisita da un nodo di calcolo. Il servizio di aumento e riduzione automatico è abilitato con intervalli di aumento e riduzione predefiniti.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### Esempio 2

Il file di configurazione seguente distribuisce un cluster HPC Pack in una foresta di domini esistente. Il cluster contiene 1 nodo head, 1 server di database con un disco dati da 500 GB, 2 nodi broker che eseguono il sistema operativo Windows Server 2012 R2 e 5 nodi di calcolo che eseguono il sistema operativo Windows Server 2012 R2. Il servizio cloud MyHPCCNService viene creato nel gruppo di affinità MyIBAffinityGroup e tutti gli altri servizi cloud vengono creati nel gruppo di affinità MyAffinityGroup. L'API REST del pianificatore di processi HPC e il portale Web di HPC sono abilitati nel nodo head.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### Esempio 3

Il file di configurazione seguente crea una nuova foresta di domini e distribuisce un cluster HPC Pack con 1 nodo head con database locali e 20 nodi di calcolo Linux. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. I nodi di calcolo Linux vengono creati in 4 servizi cloud e 4 account di archiviazione (ad esempio, da MyLnxCN-0001 a MyHPCCN-0005 in MyLnxCNService01 e mylnxstorage01, da MyLnxCN-0006 a MyLnxCN-0010 in MyLnxCNService02 e mylnxstorage02, da MyLnxCN-0011 a MyLnxCN-0015 in MyLnxCNService03 e mylnxstorage03 e da MyLnxCN-0016 a MyLnxCN-0020 in MyLnxCNService04 e mylnxstorage04). I nodi di calcolo sono creati da un'immagine OpenLogic CentOS versione 7.0.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```


### Esempio 4

Il file di configurazione seguente distribuisce un cluster HPC Pack che include 1 nodo head con database locali e 5 nodi di calcolo che eseguono il sistema operativo Windows Server 2008 R2. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. Il nodo head funge da controller di dominio della foresta di domini.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Esempio 5

Il file di configurazione seguente distribuisce un cluster HPC Pack in una foresta di domini esistente. Il cluster include 1 nodo head con database locali, vengono creati 2 modelli di nodo di Azure e vengono creati 3 nodi di Azure di dimensione Medium per il modello di nodo di Azure AzureTemplate1. Un file di script verrà eseguito sul nodo head una volta configurato il nodo.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```
## Problemi noti


* **Errore di tipo "La rete virtuale non esiste"**: se si esegue lo script di distribuzione IaaS di HPC Pack per distribuire più cluster contemporaneamente in una sottoscrizione di Azure, è possibile che una o più distribuzioni abbiano esito negativo con un errore di tipo "La rete virtuale *Nome\_rete\_virtuale* non esiste". Se si verifica questo errore, eseguire di nuovo lo script per la distribuzione non riuscita.

* **Problemi di accesso a Internet dalla rete virtuale di Azure**: se si crea un cluster HPC Pack con un nuovo controller di dominio usando lo script di distribuzione oppure se si alza di livello manualmente una macchina virtuale a controller di dominio, potrebbero verificarsi problemi di connessione a Internet delle macchine virtuali nella rete virtuale di Azure. Ciò si può verificare se un server DNS di inoltro viene configurato automaticamente nel controller di dominio e tale server non viene risolto correttamente.

    Per risolvere il problema, accedere al controller di dominio e rimuovere l'impostazione di configurazione di inoltro oppure configurare un server DNS di inoltro valido. A tale scopo, in Server Manager fare clic su **Strumenti** > **DNS** per aprire il Gestore DNS, quindi fare doppio clic su **Server d'inoltro**.

* **Problemi di accesso alla rete RDMA da macchine virtuali di dimensioni A8 o A9**: se si aggiungono macchine virtuali di un nodo di calcolo o un nodo broker Windows Server con dimensioni A8 o A9 usando lo script di distribuzione, è possibile che si verifichino problemi di connessione delle macchine virtuali alla rete dell'applicazione RDMA. Una delle cause di questo problema può essere l'installazione non corretta dell'estensione HpcVmDrivers durante l'aggiunta delle VM di dimensioni A8 o A9 al cluster. È ad esempio possibile che l'estensione sia bloccata nello stato di installazione.

    Per risolvere questo problema, verificare prima di tutto lo stato dell'estensione nelle VM. Se l'estensione non è installata correttamente, provare a rimuovere i nodi dal cluster HPC, quindi aggiungerli di nuovo. È ad esempio possibile aggiungere macchine virtuali del nodo di calcolo eseguendo lo script Add-HpcIaaSNode.ps1.


## Passaggi successivi

* Provare a eseguire un carico di lavoro di test nel cluster. Per un esempio, vedere la [guida introduttiva](https://technet.microsoft.com/library/jj884144) di HPC Pack.

* Per un'esercitazione che usa lo script per creare un cluster ed eseguire un carico di lavoro HPC, vedere [Introduzione all'uso di un cluster HPC Pack in Azure per l'esecuzione di carichi di lavoro di Excel e SOA](virtual-machines-excel-cluster-hpcpac) o [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-cluster-hpcpack-namd.md).

* Provare gli strumenti di HPC Pack per avviare, arrestare e rimuovere nodi di calcolo da un cluster. Vedere [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-hpcpack-cluster-node-manage.md)

<!---HONumber=Oct15_HO3-->