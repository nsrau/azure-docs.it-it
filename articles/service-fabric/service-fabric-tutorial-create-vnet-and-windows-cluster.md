---
title: Creare un cluster Windows di Service Fabric in Azure | Microsoft Docs
description: "In questa esercitazione si apprenderà come distribuire un cluster Windows di Service Fabric in una rete virtuale di Azure esistente tramite PowerShell."
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
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 7418e0420b14f044bac253046a8971d1263e45b3
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Esercitazione: Distribuire un cluster Windows di Service Fabric in una rete virtuale di Azure
Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un cluster di Service Fabric che esegue Windows in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) e in un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) usando PowerShell e un modello. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni.  Per creare un cluster Linux usando l'interfaccia della riga di comando di Azure, vedere come [creare un cluster Linux protetto in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Questa esercitazione descrive uno scenario di produzione.  Se si vuole creare rapidamente un cluster di piccole dimensioni a scopo di test, vedere l'articolo su come [creare un cluster di test a tre nodi](./scripts/service-fabric-powershell-create-test-cluster.md).

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
> * [Aumentare o ridurre un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Le procedure seguenti creano un cluster di Service Fabric a cinque nodi. Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Concetti chiave
Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine. Un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento.

Un tipo di nodo definisce le dimensioni, il numero e le proprietà di un set di macchine virtuali nel cluster. Ogni tipo di nodo definito viene configurato come [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/), una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. I tipi di nodo vengono usati per definire i ruoli relativi a un set di nodi del cluster, ad esempio "front-end" o "back-end".  Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario deve essere costituito da almeno cinque macchine virtuali per i cluster di produzione (o da almeno tre macchine virtuali per i cluster di test).  I [servizi di sistema Service Fabric](service-fabric-technical-overview.md#system-services) vengono inseriti nei nodi del tipo di nodo primario.

Il cluster è protetto con un certificato cluster. Un certificato del cluster è un certificato X.509 usato per proteggere le comunicazioni da nodo a nodo e autenticare gli endpoint di gestione dei cluster in un client di gestione.  Il certificato del cluster fornisce anche un certificato SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS. I certificati autofirmati sono utili per i cluster di test.  Come certificato per i cluster di produzione usare un certificato di un'entità di certificazione (CA).

Il certificato del cluster deve:

- contenere una chiave privata;
- essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange);
- avere un nome del soggetto corrispondente al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio .cloudapp.azure.com. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

L'insieme di credenziali delle chiavi di Azure viene usato per gestire i certificati dei cluster di Service Fabric in Azure.  Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati dall'insieme di credenziali delle chiavi e li installa nelle macchine virtuali del cluster.

Questa esercitazione visualizza un cluster con cinque nodi in un tipo a nodo singolo. La [pianificazione della capacità](service-fabric-cluster-capacity.md), tuttavia, è un passaggio importante per qualsiasi distribuzione di cluster di produzione. Di seguito sono elencati alcuni aspetti da considerare nell'ambito di questo processo.

- Numero e tipi di nodi necessari per il cluster 
- Proprietà di ogni tipo di nodo (ad esempio, dimensione, tipo primario, tipo per Internet e numero di macchine virtuali)
- Caratteristiche di affidabilità e durabilità del cluster

## <a name="download-and-explore-the-template"></a>Scaricare ed esplorare il modello
Scaricare i file del modello di Resource Manager seguenti:
- [vnet-cluster.json][template]
- [vnet-cluster.parameters.json][parameters]

[vnet-cluster.json][template] distribuisce alcune risorse, incluse le seguenti. 

### <a name="service-fabric-cluster"></a>Cluster di Service Fabric
Viene distribuito un cluster Windows con le caratteristiche seguenti.
- Unico tipo di nodo 
- Cinque nodi del tipo di nodo primario (configurabile nei parametri del modello)
- Servizio operativo Windows Server 2016 Datacenter con contenitori (configurabile nei parametri del modello)
- Protezione con certificato (configurabile nei parametri del modello)
- [Proxy inverso](service-fabric-reverseproxy.md) abilitato
- [Servizio DNS](service-fabric-dnsservice.md) abilitato
- [Livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronzo (configurabile nei parametri del modello)
- [Livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Argento (configurabile nei parametri del modello)
- Endpoint di connessione client: 19000 (configurabile nei parametri del modello)
- Endpoint del gateway HTTP: 19080 (configurabile nei parametri del modello)

### <a name="azure-load-balancer"></a>Servizio di bilanciamento del carico di Azure
Viene distribuito un servizio di bilanciamento del carico e vengono configurati probe e regole per le porte seguenti:
- Endpoint di connessione client: 19000
- Endpoint del gateway HTTP: 19080 
- Porta applicazione: 80
- Porta applicazione: 443
- Proxy inverso di Service Fabric: 19081

Se sono necessarie altre porte dell'applicazione, si dovranno modificare le risorse Microsoft.Network/loadBalancers e Microsoft.Network/networkSecurityGroups in modo da consentire il traffico in ingresso.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rete virtuale, subnet e gruppo di sicurezza di rete
I nomi della rete virtuale, della subnet e del gruppo di sicurezza di rete vengono dichiarati nei parametri del modello,  così come gli spazi indirizzi della rete virtuale e della subnet.
- Spazio indirizzi della rete virtuale: 172.16.0.0/20
- Spazio indirizzi della subnet di Service Fabric: 172.16.2.0/23

Nel gruppo di sicurezza di rete vengono abilitate le regole per il traffico in ingresso riportate di seguito. È possibile modificare i valori di porta modificando le variabili del modello.
- Endpoint di connessione client (TCP): 19000
- Endpoint del gateway HTTP (HTTP/TCP): 19080
- SMB: 445
- Comunicazione tra nodi: 1025, 1026, 1027
- Intervallo di porte temporaneo: da 49152 a 65534 (sono necessarie almeno 256 porte)
- Porte utilizzabili per l'applicazione: 80 e 443
- Intervallo di porte dell'applicazione: da 49152 a 65534 (quelle usate per la comunicazione tra servizi e di altro tipo non vengono aperte nel servizio di bilanciamento del carico)
- Bloccare tutte le altre porte

Se sono necessarie altre porte dell'applicazione, si dovranno modificare le risorse Microsoft.Network/loadBalancers e Microsoft.Network/networkSecurityGroups in modo da consentire il traffico in ingresso.

## <a name="set-template-parameters"></a>Impostare i parametri del modello
Nel file dei parametri [vnet-cluster.parameters.json][parameters] vengono dichiarati molti valori usati per distribuire il cluster e le risorse associate. Di seguito sono riportati alcuni dei parametri che potrebbe essere necessario modificare per la propria distribuzione:

|Parametro|Valore di esempio|Note|
|---|---||
|adminUserName|vmadmin| Nome utente amministratore per le VM del cluster. |
|adminPassword|Password#1234| Password amministratore per le VM del cluster.|
|clusterName|mysfcluster123| Nome del cluster. |
|location|southcentralus| Località del cluster. |
|certificateThumbprint|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore di identificazione personale del certificato, ad esempio "6190390162C988701DB5676EB81083EA608DCCF3"</p>. | 
|certificateUrlValue|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato. </p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere l'URL del certificato, ad esempio "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore dell'insieme di credenziali di origine, ad esempio "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuire la rete virtuale e il cluster
Configurare quindi la topologia di rete e distribuire il cluster di Service Fabric. Il modello di Resource Manager [vnet-cluster.json][template] crea una rete virtuale, nonché una subnet e un gruppo di sicurezza di rete (NSG) per Service Fabric. Il modello distribuisce anche un cluster in cui è abilitata la sicurezza basata su certificati.  Come certificato per i cluster di produzione usare un certificato di un'entità di certificazione (CA). Per proteggere i cluster di test è possibile usare un certificato autofirmato.

### <a name="create-a-cluster-using-an-existing-certificate"></a>Creare un cluster usando un certificato esistente
Lo script seguente usa il cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e un modello per distribuire un nuovo cluster in Azure. Il cmdlet crea anche un nuovo insieme di credenziali delle chiavi in Azure e carica il certificato. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-linuxcluster.json" `
-ParameterFile "$templatepath\vnet-linuxcluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Creare un cluster usando un nuovo certificato autofirmato
Lo script seguente usa il cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e un modello per distribuire un nuovo cluster in Azure. Il cmdlet crea anche un insieme di credenziali delle chiavi in Azure, aggiunge un nuovo certificato autofirmato all'insieme di credenziali delle chiavi e scarica il file del certificato in locale. 

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-linuxcluster.json" `
-ParameterFile "$templatepath\vnet-linuxcluster.parameters.json" -CertificatePassword $certpwd `
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
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
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
Gli altri articoli di questa serie di esercitazioni usano il cluster che è stato creato. Se non si intende passare subito all'articolo successivo, è opportuno eliminare il cluster e l'insieme di credenziali delle chiavi per evitare di sostenere costi. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

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


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
