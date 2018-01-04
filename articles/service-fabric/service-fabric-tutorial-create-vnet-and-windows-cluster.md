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
ms.date: 11/02/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f043ad753560ecac83324a259341a69ffce01fa5
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Distribuire un cluster Windows di Service Fabric in una rete virtuale di Azure
Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un cluster di Service Fabric basato su Windows in una rete virtuale e in una subnet di Azure esistente tramite PowerShell. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni.  Per creare un cluster Linux usando l'interfaccia della riga di comando di Azure, vedere come [creare un cluster Linux protetto in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

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
> * [Aumentare o ridurre un cluster](/service-fabric-tutorial-scale-cluster.md)
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Le procedure seguenti creano un cluster di Service Fabric a cinque nodi. Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="introduction"></a>Introduzione
Questa esercitazione distribuisce un cluster di cinque nodi in un unico tipo di nodo in una rete virtuale di Azure.

Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine. Un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento.

Un tipo di nodo definisce le dimensioni, il numero e le proprietà di un set di macchine virtuali nel cluster. Ogni tipo di nodo definito viene configurato come [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/), una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. I tipi di nodo vengono usati per definire i ruoli relativi a un set di nodi del cluster, ad esempio "front-end" o "back-end".  Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario deve essere costituito da almeno cinque macchine virtuali per i cluster di produzione (o da almeno tre macchine virtuali per i cluster di test).  I [servizi di sistema Service Fabric](service-fabric-technical-overview.md#system-services) vengono inseriti nei nodi del tipo di nodo primario.

## <a name="cluster-capacity-planning"></a>Pianificazione della capacità dei cluster
Questa esercitazione distribuisce un cluster di cinque nodi in un unico tipo di nodo.  La pianificazione della capacità è un passaggio importante per qualsiasi distribuzione di un cluster di produzione. Di seguito sono elencati alcuni aspetti da considerare nell'ambito di questo processo.

- Numero di tipi di nodo necessari per il cluster 
- Proprietà di ogni tipo di nodo (ad esempio, dimensione, tipo primario, tipo per Internet e numero di macchine virtuali)
- Caratteristiche di affidabilità e durabilità del cluster

Per altre informazioni, vedere [Considerazioni sulla pianificazione della capacità del cluster](service-fabric-cluster-capacity.md).

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
Configurare quindi la topologia di rete nella quale verrà distribuito il cluster di Gestione API e Service Fabric. Il modello di Gestione risorse [network.json][network-arm] è configurato per creare una rete virtuale (VNET), nonché una subnet e un gruppo di sicurezza di rete (NSG) per Service Fabric e una subnet e un gruppo di sicurezza di rete (NSG) per Gestione API. Gestione API viene distribuito più avanti in questa esercitazione. Altre informazioni sulle reti virtuali, le subnet e NSG sono reperibili [qui](../virtual-network/virtual-networks-overview.md).

Il file dei parametri [network.parameters.json][network-parameters-arm] contiene i nomi delle subnet e dei gruppi di sicurezza di rete in cui verranno distribuiti Service Fabric e Gestione API.  Gestione API viene distribuito nell'[esercitazione seguente](service-fabric-tutorial-deploy-api-management.md). In questa guida non è necessario modificare i valori dei parametri. I modelli di Gestione risorse di Service Fabric usano questi valori.  Se i valori vengono modificati qui, è necessario modificarli negli altri modelli di Gestione risorse usati in questa esercitazione e nell'[esercitazione di distribuzione di Gestione API](service-fabric-tutorial-deploy-api-management.md). 

Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Per distribuire il modello e il file di parametri di Resource Manager per la configurazione di rete, usare il comando PowerShell seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>Distribuire il cluster di Service Fabric
Una volta terminata la distribuzione delle risorse di rete, il passaggio successivo consiste nel distribuire un cluster Service Fabric per la rete virtuale nella subnet e nel gruppo NSG designato per tale cluster. La distribuzione di un cluster in una rete virtuale esistente e una subnet (distribuito in precedenza in questo articolo) richiede un modello di Gestione risorse.  Per questa serie di esercitazioni, il modello è preconfigurato per usare i nomi di rete virtuale, subnet e gruppo di sicurezza di rete configurati in un passaggio precedente.  

Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Usare questo modello per creare un cluster protetto.  Un certificato del cluster è un certificato X.509 usato per proteggere le comunicazioni da nodo a nodo e autenticare gli endpoint di gestione dei cluster in un client di gestione.  Il certificato del cluster fornisce anche un certificato SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS. L'insieme di credenziali delle chiavi di Azure viene usato per gestire i certificati dei cluster di Service Fabric in Azure.  Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati dall'insieme di credenziali delle chiavi e li installa nelle macchine virtuali del cluster. 

È possibile usare un certificato da un'autorità di certificazione come certificato del cluster oppure, a scopo di test, creare un certificato autofirmato. Il certificato del cluster deve:

- contenere una chiave privata;
- essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange);
- avere un nome del soggetto corrispondente al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio .cloudapp.azure.com. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

Specificare un valore per questi parametri vuoti nel file *cluster.parameters.json* per la distribuzione:

|Parametro|Valore|
|---|---|
|adminPassword|Password#1234|
|adminUserName|vmadmin|
|clusterName|mysfcluster|
|location|southcentralus|

Lasciare vuoti i parametri *certificateThumbprint*, *certificateUrlValue* e *sourceVaultValue* per creare un certificato autofirmato.  Per usare un certificato esistente caricato in precedenza in un insieme di credenziali delle chiavi, immettere quei valori di parametro.

Lo script seguente usa il cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e il modello per distribuire un nuovo cluster in Azure. Il cmdlet crea anche un insieme di credenziali delle chiavi in Azure, aggiunge un nuovo certificato autofirmato all'insieme di credenziali delle chiavi e scarica il file del certificato in locale. È possibile specificare un certificato esistente e/o un insieme di credenziali delle chiavi con altri parametri del cmdlet [New AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster).

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
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
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando PowerShell
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Creare un cluster sicuro di Service Fabric in Azure tramite PowerShell
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come ridimensionare il cluster.
> [!div class="nextstepaction"]
> [Ridimensionare un cluster](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
