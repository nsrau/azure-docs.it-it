<properties
   pageTitle="Script PowerShell per distribuire cluster HPC Windows | Microsoft Azure"
   description="Eseguire uno script di PowerShell per distribuire un cluster HPC Pack Windows in macchine virtuali di Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# Creare un cluster Windows HPC (High Performance Computing) con lo script di distribuzione IaaS di HPC Pack

Eseguire lo script PowerShell di distribuzione IaaS di HPC Pack per distribuire un cluster HPC completo per carichi di lavoro di Windows nelle macchine virtuali di Azure. Il cluster è costituito da un nodo head aggiunto ad Active Directory che esegue Windows Server e Microsoft HPC Pack e da altre risorse di calcolo di Windows specificate. Se si desidera distribuire un cluster HPC Pack in Azure per i carichi di lavoro di Linux, vedere [Creare un cluster HPC Linux con lo script di distribuzione IaaS di HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Per distribuire un cluster HPC Pack è anche possibile usare un modello di Gestione risorse di Azure. Per esempi, vedere [Create an HPC cluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) (Creare un cluster HPC) e [Create an HPC cluster with a custom compute node image](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) (Creare un cluster HPC con un'immagine di nodo di calcolo personalizzata).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## File di configurazione di esempio

Negli esempi seguenti sostituire i propri valori per l'ID o il nome della sottoscrizione e i nomi degli account e dei servizi.

### Esempio 1

Il file di configurazione seguente distribuisce un cluster HPC Pack che include un nodo head con database locali e 5 nodi di calcolo che eseguono il sistema operativo Windows Server 2012 R2. Tutti i servizi cloud vengono creati direttamente nell'area Stati Uniti occidentali. Il nodo head funge da controller di dominio della foresta di domini.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
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
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Esempio 2

Il file di configurazione seguente distribuisce un cluster HPC Pack in una foresta di domini esistente. Il cluster include 1 nodo head con database locali e 12 nodi di calcolo con l'estensione BGInfo applicata. L'installazione automatica degli aggiornamenti di Windows è disabilitata per tutte le macchine virtuali nella foresta di domini. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. I nodi di calcolo vengono creati in 3 servizi cloud e 3 account di archiviazione, ovvero da _MyHPCCN-0001_ a _MyHPCCN-0005_ in _MyHPCCNService01_ e _mycnstorage01_, da _MyHPCCN-0006_ a _MyHPCCN0010_ in _MyHPCCNService02_ e _mycnstorage02_ e infine da _MyHPCCN-0011_ a _MyHPCCN-0012_ in _MyHPCCNService03_ e _mycnstorage03_. I nodi di calcolo vengono creati da un'immagine privata esistente acquisita da un nodo di calcolo. Il servizio di aumento e riduzione automatico è abilitato con intervalli di aumento e riduzione predefiniti.

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

### Esempio 3

Il file di configurazione seguente distribuisce un cluster HPC Pack in una foresta di domini esistente. Il cluster contiene 1 nodo head, 1 server di database con un disco dati da 500 GB, 2 nodi broker che eseguono il sistema operativo Windows Server 2012 R2 e 5 nodi di calcolo che eseguono il sistema operativo Windows Server 2012 R2. Il servizio cloud MyHPCCNService viene creato nel gruppo di affinità *MyIBAffinityGroup* e tutti gli altri servizi cloud vengono creati nel gruppo di affinità *MyAffinityGroup*. L'API REST del pianificatore di processi HPC e il portale Web di HPC sono abilitati nel nodo head.

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



### Esempio 4

Il file di configurazione seguente distribuisce un cluster HPC Pack in una foresta di domini esistente. Il cluster include 1 nodo head con database locali, vengono creati due modelli di nodo di Azure e 3 nodi di Azure di dimensione Media per il modello di nodo di Azure _AzureTemplate1_. Un file di script verrà eseguito sul nodo head una volta configurato il nodo.

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

## Risoluzione dei problemi


* **Errore di tipo "La rete virtuale non esiste"**: se si esegue lo script di distribuzione IaaS di HPC Pack per distribuire più cluster contemporaneamente in una sottoscrizione di Azure, è possibile che una o più distribuzioni abbiano esito negativo con un errore di tipo "La rete virtuale *Nome\_rete\_virtuale* non esiste". Se si verifica questo errore, eseguire di nuovo lo script per la distribuzione non riuscita.

* **Problemi di accesso a Internet dalla rete virtuale di Azure**: se si crea un cluster HPC Pack con un nuovo controller di dominio usando lo script di distribuzione oppure se si alza di livello manualmente una macchina virtuale di un nodo head a controller di dominio, potrebbero verificarsi problemi di connessione a Internet delle macchine virtuali nella rete virtuale di Azure. Ciò si può verificare se un server DNS di inoltro viene configurato automaticamente nel controller di dominio e tale server non viene risolto correttamente.

    Per risolvere il problema, accedere al controller di dominio e rimuovere l'impostazione di configurazione di inoltro oppure configurare un server DNS di inoltro valido. A tale scopo, in Server Manager fare clic su **Strumenti** > **DNS** per aprire il Gestore DNS, quindi fare doppio clic su **Server d'inoltro**.

* **Problemi di accesso alla rete RDMA da macchine virtuali di dimensioni A8 o A9**: se si aggiungono macchine virtuali di un nodo di calcolo o un nodo broker Windows Server con dimensioni A8 o A9 usando lo script di distribuzione, è possibile che si verifichino problemi di connessione delle macchine virtuali alla rete dell'applicazione RDMA. Una delle cause di questo problema può essere l'installazione non corretta dell'estensione HpcVmDrivers durante l'aggiunta delle VM di dimensioni A8 o A9 al cluster. È ad esempio possibile che l'estensione sia bloccata nello stato di installazione.

    Per risolvere questo problema, verificare prima di tutto lo stato dell'estensione nelle VM. Se l'estensione non è installata correttamente, provare a rimuovere i nodi dal cluster HPC, quindi aggiungerli di nuovo. È ad esempio possibile aggiungere macchine virtuali del nodo di calcolo eseguendo lo script Add-HpcIaaSNode.ps1.
    
## Passaggi successivi

* Provare a eseguire un carico di lavoro di test nel cluster. Per un esempio, vedere la [guida introduttiva](https://technet.microsoft.com/library/jj884144) di HPC Pack.

* Per un'esercitazione che usa lo script per creare un cluster ed eseguire un carico di lavoro HPC, vedere [Introduzione all'uso di un cluster HPC Pack in Azure per l'esecuzione di carichi di lavoro di Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Provare gli strumenti di HPC Pack per avviare, arrestare e rimuovere nodi di calcolo da un cluster. Vedere [Gestire il numero e la disponibilità dei nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Per configurare l'invio di processi al cluster da un computer locale, vedere [Inviare i processi HPC da un computer locale a un cluster HPC Pack distribuito in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

<!---HONumber=AcomDC_0713_2016-->