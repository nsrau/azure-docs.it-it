---
title: Creare un cluster Windows di Service Fabric in Azure | Microsoft Docs
description: Informazioni su come distribuire un cluster Windows di Service Fabric in una rete virtuale di Azure esistente tramite PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: b3bab57f5ca6627b4532284376a9809d5ab543f2
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Distribuire un cluster Windows di Service Fabric in una rete virtuale di Azure
Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un cluster Windows di Service Fabric in una rete virtuale e in una subnet di Azure esistente tramite PowerShell. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni.  Per creare un cluster Linux usando l'interfaccia della riga di comando di Azure, vedere come [creare un cluster Linux protetto in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando PowerShell
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Creare un cluster sicuro di Service Fabric in Azure PowerShell
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster sicuro in Azure
> * [Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Le procedure seguenti creano un cluster di Service Fabric a cinque nodi. Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Accedere ad Azure e selezionare la sottoscrizione
Questa guida usa Azure PowerShell. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.
 
Eseguire questo script per accedere al proprio account Azure e selezionare la sottoscrizione:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un nuovo gruppo di risorse per la distribuzione e assegnargli un nome e un percorso.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>Distribuire la topologia di rete
Configurare quindi la topologia di rete nella quale verrà distribuito il cluster di Gestione API e Service Fabric. Il modello di Gestione risorse [network.json][network-arm] è configurato per creare una rete virtuale (VNET), nonché una subnet e un gruppo di sicurezza di rete (NSG) per Service Fabric e una subnet e un gruppo di sicurezza di rete (NSG) per Gestione API. Altre informazioni sulle reti virtuali, le subnet e NSG sono reperibili [qui](../virtual-network/virtual-networks-overview.md).

Il file dei parametri [network.parameters.json][network-parameters-arm] contiene i nomi delle subnet e dei gruppi di sicurezza di rete in cui verranno distribuiti Service Fabric e Gestione API.  Gestione API viene distribuito nell'[esercitazione seguente](service-fabric-tutorial-deploy-api-management.md). In questa guida non è necessario modificare i valori dei parametri. I modelli di Gestione risorse di Service Fabric usano questi valori.  Se i valori vengono modificati qui, è necessario modificarli negli altri modelli di Gestione risorse usati in questa esercitazione e nell'[esercitazione di distribuzione di Gestione API](service-fabric-tutorial-deploy-api-management.md). 

Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Per distribuire il modello e il file di parametri di Resource Manager per la configurazione di rete, usare il comando PowerShell seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Distribuire il cluster di Service Fabric
Una volta terminata la distribuzione delle risorse di rete, il passaggio successivo consiste nel distribuire un cluster Service Fabric per la rete virtuale nella subnet e nel gruppo NSG designato per tale cluster. La distribuzione di un cluster in una rete virtuale esistente e una subnet (distribuito in precedenza in questo articolo) richiede un modello di Gestione risorse.  Per altre informazioni, vedere [Creare un cluster usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Per questa serie di esercitazioni, il modello è preconfigurato per usare i nomi di rete virtuale, subnet e gruppo di sicurezza di rete configurati in un passaggio precedente.  Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Il certificato viene usato per proteggere la comunicazione da nodo a nodo per il cluster e gestire l'accesso utente al cluster di Service Fabric. Gestione API usa inoltre questo certificato per accedere al Service Fabric Naming Service per l'individuazione del servizio. 

Lo script seguente usa il cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) per distribuire un nuovo cluster in Azure. Il cmdlet crea anche un insieme di credenziali delle chiavi in Azure, un certificato autofirmato e l'insieme di credenziali delle chiavi e scarica il file del certificato in locale.   

```powershell
# Certificate variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"

# Variables for VM admin.
$adminuser="vmadmin"
$adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

# Variables for common values
$clustername = "mysfcluster"
$vmsku = "Standard_D2_v2"
$vaultname = "clusterkeyvault"
$vaultgroupname="clusterkeyvaultgroup"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. Possible values: 1, 3-99
$clustersize=5 

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname `
-TemplateFile .\cluster.json -ParameterFile .\cluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro
Connettersi al cluster usando il modulo di PowerShell Service Fabric installato con Service Fabric SDK.  Installare prima di tutto il certificato nell'archivio personale (My) dell'utente corrente nel computer in uso.  Eseguire il seguente comando PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

È ora possibile connettersi al cluster sicuro.

Il modulo di PowerShell **Service Fabric** include molti cmdlet per la gestione di cluster, applicazioni e servizi di Service Fabric.  Usare il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) per connettersi al cluster sicuro. L'identificazione personale del certificato e i dettagli dell'endpoint della connessione sono disponibili nel risultato del passaggio precedente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verificare di essere connessi e che il cluster sia integro usando il cmdlet [Get ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Pulire le risorse
Gli altri articoli in questa serie di esercitazioni usano il cluster appena creato. Se non si intende passare subito all'articolo successivo, è opportuno eliminare il cluster e l'insieme di credenziali delle chiavi per evitare di sostenere costi. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Eliminare il gruppo di risorse e tutte le risorse del cluster con il [cmdlet Remove-AzureRmResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  Eliminare anche il gruppo di risorse che contiene l'insieme di credenziali delle chiavi.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando PowerShell
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Creare un cluster sicuro di Service Fabric in Azure tramite PowerShell
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come distribuire Gestione API con Service Fabric.
> [!div class="nextstepaction"]
> [Distribuire Gestione API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
