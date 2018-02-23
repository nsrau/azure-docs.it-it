---
title: Script PowerShell per distribuire cluster HPC Linux | Documentazione Microsoft
description: Eseguire uno script PowerShell per distribuire un cluster HPC Pack 2012 R2 Linux in macchine virtuali di Azure
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
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: c15dc66718a855e22f8109448cb8c8a23787b9bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Creare un cluster HPC (High Performance Computing) Linux con lo script di distribuzione IaaS di HPC Pack
Eseguire lo script PowerShell di distribuzione IaaS di HPC Pack per distribuire un cluster HPC Pack 2012 R2 completo per carichi di lavoro Linux in macchine virtuali di Azure. Il cluster è costituito da un nodo head aggiunto ad Active Directory che esegue Windows Server e Microsoft HPC Pack e da nodi di calcolo che eseguono una delle distribuzioni di Linux supportate da HPC Pack. Se si desidera distribuire un cluster HPC Pack in Azure per i carichi di lavoro di Windows, vedere [Creare un cluster Windows HPC con lo script di distribuzione IaaS di HPC Pack](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Per distribuire un cluster HPC Pack è anche possibile usare un modello di Gestione risorse di Azure. Per un esempio, vedere [Creare un cluster HPC con nodi di calcolo Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

> [!IMPORTANT] 
> Lo script PowerShell descritto in questo articolo crea un cluster Microsoft HPC Pack 2012 R2 in Azure usando il modello di distribuzione classico. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
> Lo script descritto in questo articolo inoltre non supporta HPC Pack 2016.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>File di configurazione di esempio
Il file di configurazione seguente crea una foresta di domini e di controller di dominio e distribuisce un cluster HPC Pack con un nodo head con database locali e 10 nodi di calcolo Linux. Tutti i servizi cloud vengono creati direttamente nell'area Asia orientale. I nodi di calcolo Linux vengono creati in due servizi cloud e due account di archiviazione (vale a dire *MyLnxCN-0001* a *MyLnxCN-0005* in *MyLnxCNService01* e *mylnxstorage01* e *MyLnxCN-0006* a *MyLnxCN-0010* in *MyLnxCNService02* e *mylnxstorage02*). I nodi di calcolo sono creati da un'immagine OpenLogic CentOS versione 7.0. 

Sostituire con i propri valori il nome della sottoscrizione e i nomi degli account e dei servizi.

```Xml
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
## <a name="troubleshooting"></a>risoluzione dei problemi
* **Errore "La rete virtuale non esiste"**. Se si esegue lo script di distribuzione IaaS di HPC Pack per distribuire istantaneamente più cluster in Azure in una sottoscrizione, è possibile che una o più distribuzioni generi l'errore "La rete virtuale *Nome\_Retevirtuale* non esiste".
  In questo caso, eseguire nuovamente lo script per la distribuzione non riuscita.
* **Problemi di accesso a Internet dalla rete virtuale di Azure**. Se si crea un cluster HPC Pack con un nuovo controller di dominio usando lo script di distribuzione oppure se si alza manualmente di livello la VM di un nodo head trasformandola in controller di dominio, è possibile che si verifichino problemi di connessione a Internet delle VM nella rete virtuale di Azure. Ciò si può verificare se un server DNS di inoltro viene configurato automaticamente nel controller di dominio e tale server non viene risolto correttamente.
  
    Per risolvere il problema, accedere al controller di dominio e rimuovere l'impostazione di configurazione di inoltro oppure configurare un server DNS di inoltro valido. A tale scopo, in Server Manager fare clic su **Strumenti** > **DNS** per aprire il Gestore DNS, quindi fare doppio clic su **Server d'inoltro**.

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Introduzione all'uso di nodi di calcolo Linux in un cluster HPC Pack in Azure](hpcpack-cluster.md) per informazioni sulle distribuzioni Linux supportate, lo spostamento dei dati e inviare processi a un cluster HPC Pack con Linux nodi di calcolo.
* Per esercitazioni in cui si usa lo script per creare un cluster ed eseguire un carico di lavoro HPC Linux, vedere:
  * [Eseguire NAMD con Microsoft HPC Pack su nodi di calcolo Linux in Azure](hpcpack-cluster-namd.md)
  * [Eseguire OpenFoam con Microsoft HPC Pack su nodi di calcolo Linux in Azure](hpcpack-cluster-openfoam.md)
  * [Eseguire STAR-CCM+ con Microsoft HPC Pack su nodi di calcolo Linux in Azure](hpcpack-cluster-starccm.md)

