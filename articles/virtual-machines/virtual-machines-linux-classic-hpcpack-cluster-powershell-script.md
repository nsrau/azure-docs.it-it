<properties
   pageTitle="Script PowerShell per distribuire cluster HPC Linux | Microsoft Azure"
   description="Eseguire uno script di PowerShell per distribuire un cluster HPC Pack Linux in servizi di infrastruttura di Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="04/05/2016"
   ms.author="danlep"/>

# Creare un cluster HPC (High Performance Computing) Linux con lo script di distribuzione IaaS di HPC Pack

Eseguire lo script PowerShell di distribuzione IaaS di HPC Pack in un computer client per distribuire un cluster HPC completo per i carichi di lavoro Linux nei servizi di infrastruttura di Azure (IaaS). Se si desidera distribuire un cluster HPC Pack in Azure per i carichi di lavoro di Windows, vedere [Creare un cluster Windows HPC con lo script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## File di configurazione di esempio

### Esempio 1

Il file di configurazione seguente crea una nuova foresta di domini e distribuisce un cluster HPC Pack con 1 nodo head con database locali e 20 nodi di calcolo Linux. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. I nodi di calcolo Linux vengono creati in 4 servizi cloud e 4 account di archiviazione (ovvero da _MyLnxCN-0001_ a _MyLnxCN-0005_ in _MyLnxCNService01_ e _mylnxstorage01_, da _MyLnxCN-0006_ a _MyLnxCN-0010_ in _MyLnxCNService02_ e _mylnxstorage02_, da _MyLnxCN-0011_ a _MyLnxCN-0015_ in _MyLnxCNService03_ e _mylnxstorage03_ e da _MyLnxCN-0016_ a _MyLnxCN-0020_ in _MyLnxCNService04_ e _mylnxstorage04_). I nodi di calcolo sono creati da un'immagine OpenLogic CentOS versione 7.0.

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
## Risoluzione dei problemi

* **Errore di tipo "La rete virtuale non esiste"**: se si esegue lo script di distribuzione IaaS di HPC Pack per distribuire più cluster contemporaneamente in una sottoscrizione di Azure, è possibile che una o più distribuzioni abbiano esito negativo con un errore di tipo "La rete virtuale *Nome\_rete\_virtuale* non esiste". Se si verifica questo errore, eseguire di nuovo lo script per la distribuzione non riuscita.

* **Problemi di accesso a Internet dalla rete virtuale di Azure**: se si crea un cluster HPC Pack con un nuovo controller di dominio usando lo script di distribuzione oppure se si alza di livello manualmente una macchina virtuale di un nodo head a controller di dominio, potrebbero verificarsi problemi di connessione a Internet delle macchine virtuali nella rete virtuale di Azure. Ciò si può verificare se un server DNS di inoltro viene configurato automaticamente nel controller di dominio e tale server non viene risolto correttamente.

    Per risolvere il problema, accedere al controller di dominio e rimuovere l'impostazione di configurazione di inoltro oppure configurare un server DNS di inoltro valido. A tale scopo, in Server Manager fare clic su **Strumenti** > **DNS** per aprire il Gestore DNS, quindi fare doppio clic su **Server d'inoltro**.
    
## Passaggi successivi

* Per esercitazioni che usano lo script per creare un cluster ed eseguire un carico di lavoro HPC Linux, vedere [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md) o [Eseguire OpenFOAM con Microsoft HPC Pack in su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).

* Per distribuire un cluster HPC Pack è anche possibile usare un modello di Gestione risorse di Azure. Per un esempio, vedere [Creare un cluster HPC con nodi di calcolo Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

<!---HONumber=AcomDC_0629_2016-->