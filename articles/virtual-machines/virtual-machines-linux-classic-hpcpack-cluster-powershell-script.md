---
title: Script PowerShell per distribuire cluster HPC Linux | Microsoft Docs
description: Eseguire uno script di PowerShell per distribuire un cluster HPC Pack Linux in macchine virtuali di Azure
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f2b4081a0a5b158714cb016fd46570a19dae5d00


---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Creare un cluster HPC (High Performance Computing) Linux con lo script di distribuzione IaaS di HPC Pack
Eseguire lo script PowerShell di distribuzione IaaS di HPC Pack per distribuire un cluster HPC completo per carichi di lavoro di Linux nelle macchine virtuali di Azure. Il cluster è costituito da un nodo head aggiunto ad Active Directory che esegue Windows Server e Microsoft HPC Pack e da nodi di calcolo che eseguono una delle distribuzioni di Linux supportate da HPC Pack. Se si desidera distribuire un cluster HPC Pack in Azure per i carichi di lavoro di Windows, vedere [Creare un cluster Windows HPC con lo script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Per distribuire un cluster HPC Pack è anche possibile usare un modello di Gestione risorse di Azure. Per un esempio, vedere [Creare un cluster HPC con nodi di calcolo Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>File di configurazione di esempio
Il file di configurazione seguente crea una nuova foresta di domini controller di dominio e distribuisce un cluster HPC Pack con 1 nodo head con database locali e 10 nodi di calcolo Linux. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. I nodi di calcolo Linux vengono creati in 2 servizi cloud e 2 account di archiviazione, ovvero da *MyLnxCN-0001* a *MyLnxCN-0005* in *MyLnxCNService01* e in *mylnxstorage01* e da *MyLnxCN-0006* a *MyLnxCN-0010* in *MyLnxCNService02* e in *mylnxstorage02*. I nodi di calcolo sono creati da un'immagine OpenLogic CentOS versione 7.0. 

Sostituire con i propri valori il nome della sottoscrizione e i nomi degli account e dei servizi.

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
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Risoluzione dei problemi
* **Errore di tipo "La rete virtuale non esiste"**: se si esegue lo script di distribuzione IaaS HPC Pack per distribuire più cluster contemporaneamente in una sottoscrizione di Azure, è possibile che una o più distribuzioni abbiano esito negativo con un errore di tipo "La rete virtuale *Nome\_rete virtuale* non esiste".
  Se si verifica questo errore, eseguire di nuovo lo script per la distribuzione non riuscita.
* **Problemi di accesso a Internet dalla rete virtuale di Azure** : se si crea un cluster HPC Pack con un nuovo controller di dominio usando lo script di distribuzione oppure se si alza di livello manualmente una macchina virtuale di un nodo head a controller di dominio, potrebbero verificarsi problemi di connessione a Internet delle macchine virtuali nella rete virtuale di Azure. Ciò si può verificare se un server DNS di inoltro viene configurato automaticamente nel controller di dominio e tale server non viene risolto correttamente.
  
    Per risolvere il problema, accedere al controller di dominio e rimuovere l'impostazione di configurazione di inoltro oppure configurare un server DNS di inoltro valido. A tale scopo, in Server Manager fare clic su **Strumenti** >
    **DNS** per aprire il Gestore DNS, quindi fare doppio clic su **Server d'inoltro**.

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) per informazioni sulle distribuzioni Linux supportate, lo spostamento dei dati e inviare processi a un cluster HPC Pack con Linux nodi di calcolo.
* Per esercitazioni in cui si usa lo script per creare un cluster ed eseguire un carico di lavoro HPC Linux, vedere:
  * [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
  * [Eseguire OpenFoam con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
  * [Eseguire STAR-CCM+ con Microsoft HPC Pack su nodi di calcolo Linux in Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)




<!--HONumber=Dec16_HO1-->


