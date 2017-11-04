---
title: Creare un cluster di Service Fabric in Azure | Microsoft Docs
description: Informazioni su come creare un cluster di Service Fabric per Windows o Linux in Azure usando PowerShell.
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
ms.date: 10/03/2017
ms.author: cristyg
ms.custom: mvc
ms.openlocfilehash: 29a8d5cf9a59a08dc63df1dbd601aa1f509cad95
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>Creare un cluster sicuro in Azure con PowerShell
Questo è il primo passaggio di una serie di esercitazioni che illustra come spostare un'applicazione .NET nel cloud usando i cluster di Service Fabric e i contenitori. La procedura seguente illustra come creare un cluster di Service Fabric (Windows o Linux) in esecuzione in Azure. Al termine, si ottiene un cluster sicuro in esecuzione nel cloud, nel quale è possibile distribuire applicazioni.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installare [Service Fabric SDK](service-fabric-get-started.md)
- Installare il [modulo di Azure Powershell versione 4.1 o successiva](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (se necessario, [installare Azure PowerShell](/powershell/azure/overview) o [eseguire l'aggiornamento a una versione più recente](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps))


# <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric

Questo script crea un cluster di Service Fabric di anteprima a nodo singolo. protetto da un certificato autofirmato creato insieme al cluster e posizionato in un insieme di credenziali delle chiavi. I cluster a nodo singolo non possono essere ridimensionati per più di una macchina virtuale e i cluster di anteprima non possono essere aggiornati a versioni più recenti.

Per calcolare i costi legati all'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).
Per altre informazioni sulla creazione di cluster di Service Fabric, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure
Aprire una console di PowerShell, accedere ad Azure e selezionare la sottoscrizione in cui si vuole distribuire il cluster:

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>Parametri del cluster

   Questo script usa i parametri e i concetti seguenti. Personalizzare i parametri in base alle esigenze.

   | Parametro       | Descrizione | Valore consigliato |
   | --------------- | ----------- | --------------- |
   | Percorso | Area di Azure per la distribuzione del cluster. | *ad esempio westeurope, eastasia, eastus* |
   | Nome     | Nome del cluster che si desidera creare. Il nome deve essere costituito da un numero di caratteri compreso tra 4 e 23 e può contenere solo lettere minuscole, numeri e trattini. | *ad esempio bobs-sfpreviewcluster* |
   | ResourceGroupName   | Nome del gruppo di risorse in cui creare il cluster. | *Ad esempio, gruppodirisorse* |
   | VmSku  | SKU della macchina virtuale da usare per i nodi. | *Qualsiasi SKU di macchina virtuale valido* |
   | OS  | Sistema operativo della macchina virtuale da usare per i nodi. | *Qualsiasi sistema operativo di macchina virtuale valido* |
   | KeyVaultName | Nome del nuovo insieme di credenziali delle chiavi da associare al cluster. | *Ad esempio, insiemecredenzialichiavi* |
   | ClusterSize | Il numero di macchine virtuali del cluster, può essere 1 o variare da 3 a 99.| *Specificare una sola macchina virtuale per un cluster di anteprima* |
   | CertificateSubjectName | Nome soggetto del certificato da creare. | *Usa il formato <name>.<location>.cloudapp.azure.com* |

## <a name="default-parameter-values"></a>Valori dei parametri predefiniti
Le impostazioni relative a **Macchina virtuale** sono facoltative. Se non vengono specificate, il valore predefinito del nome utente amministratore è "vmadmin" e PowerShell chiede una password della macchina virtuale prima della creazione del cluster.

Per **Porte** vengono usate le porte predefinite 80 e 8081. È possibile specificare altre porte seguendo le istruzioni relative alle [porte nei cluster di Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule).

Le opzioni di **Diagnostica** sono abilitate per impostazione predefinita.

Il **servizio DNS** è disabilitato per impostazione predefinita.

Anche il **proxy inverso** è disabilitato per impostazione predefinita.

## <a name="create-the-cluster-with-your-parameters"></a>Creare il cluster con i parametri

Una volta stabilito quali parametri soddisfano i requisiti, eseguire il comando seguente per generare un cluster di Service Fabric sicuro e il relativo certificato.

È possibile modificare questo script per includere parametri aggiuntivi. Per altre informazioni sui parametri per la creazione dei cluster, vedere il cmdlet [New AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md).

>[!NOTE]
>Prima di eseguire questo comando, è necessario creare una cartella per il certificato del cluster.

```powershell

    # Certificate variables. This will create and encrypt a password to be used by Service Fabric.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

    # You must create the folder where you want to output the certificate on your machine before executing this step.
    $certfolder="c:\mycertificates\"

    # Variables for common values. Change the values to fit your needs.
    $clusterloc="WestUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=1

    # Create the Service Fabric cluster and its self-signed certificate. The OS specified here allows you use this cluster with any applications that are also leveraging containers.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

Il processo di creazione può richiedere alcuni minuti. Al termine della configurazione, vengono generate informazioni relative al cluster creato in Azure. La configurazione copia anche il certificato del cluster nella directory CertificateOutputFolder nel percorso specificato per questo parametro.

Prendere nota dell'URL **ManagementEndpoint** per il cluster, che può essere simile all'URL seguente: *https://mycluster.westeurope.cloudapp.azure.com:19080*

## <a name="import-the-certificate"></a>Importare il certificato

Dopo la creazione del cluster, eseguire il comando seguente per verificare di poter usare il certificato autofirmato:

```powershell

    # Connect to the cluster using by installing the certificate into the Personal (My) store of the current user on your computer.
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
            -Password $certpwd
```

Questo comando installa il certificato per l'utente corrente del computer.  È necessario questo certificato per accedere a Service Fabric Explorer e visualizzare l'integrità del cluster.


## <a name="view-your-cluster-optional"></a>Visualizzare il cluster (facoltativo)

Quando si dispone del cluster e del certificato importato, è possibile connettersi al cluster e visualizzarne l'integrità. Ci sono diversi modi per connettersi, tramite Service Fabric Explorer o PowerShell.

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
È possibile visualizzare l'integrità del cluster aprendo Service Fabric Explorer. A tale scopo, passare all'URL **ManagementEndpoint** per il cluster tramite un Web browser e quindi selezionare il certificato salvato nel computer.

>[!NOTE]
>Quando si apre Service Fabric Explorer, viene visualizzato un errore di certificato, poiché si usa un certificato autofirmato. In Edge, è necessario fare clic su *Dettagli* e quindi sul collegamento *Continua per la pagina Web*. In Chrome, è necessario fare clic su *Advanced* (Impostazioni avanzate)e quindi sul collegamento *Procedi*.

### <a name="powershell"></a>PowerShell

Il modulo di PowerShell **Service Fabric** include molti cmdlet per la gestione di cluster, applicazioni e servizi di Service Fabric.  Usare il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) per connettersi al cluster sicuro. L'identificazione personale del certificato e i dettagli dell'endpoint della connessione sono disponibili nell'output di un passaggio precedente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

È anche possibile verificare di essere connessi e che il cluster sia integro usando il cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come creare un cluster sicuro di Service Fabric in Azure tramite PowerShell

Procedere con l'esercitazione seguente per scoprire come distribuire un'applicazione esistente.
> [!div class="nextstepaction"]
> [Distribuire un'app .NET tramite Docker Compose](service-fabric-host-app-in-a-container.md)

---
