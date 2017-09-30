---
title: Creare un cluster di Service Fabric in Azure | Microsoft Docs
description: Informazioni su come creare un cluster Windows in Azure usando un modello.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5d56fd468998ee4b1253b47aa133812e0141062b
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-secure-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Distribuire un cluster Windows protetto di Service Fabric in una rete virtuale di Azure
Questa è la prima di una serie di esercitazioni. Si apprenderà come creare un cluster di Service Fabric (Windows) in esecuzione in Azure e distribuirlo in una rete virtuale esistente e in una subnet. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni.  Per creare un cluster Linux, vedere [Create a secure Linux cluster on Azure using a template](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Creare un cluster Linux protetto in Azure usando un modello).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando un modello
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Creare un cluster protetto di Service Fabric in Azure usando un modello
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster protetto in Azure usando un modello
> * [Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installare [Service Fabric SDK e il modulo PowerShell](service-fabric-get-started.md)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Le procedure seguenti creano un cluster di Service Fabric a cinque nodi. Il cluster è protetto da un certificato autofirmato posizionato in un insieme di credenziali delle chiavi. 

Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).
Per altre informazioni sulla creazione di cluster di Service Fabric, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Accedere ad Azure e selezionare la sottoscrizione
Questa guida usa Azure PowerShell. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.
 
Eseguire il seguente script per accedere al proprio account Azure e selezionare la sottoscrizione:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un nuovo gruppo di risorse per la distribuzione e assegnargli un nome e un percorso.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>Distribuire la topologia di rete
Configurare quindi la topologia di rete nella quale verrà distribuito il cluster di Gestione API e Service Fabric. Il modello di Gestione risorse [network.json][network-arm] è configurato per creare una rete virtuale (VNET), nonché una subnet e un gruppo di sicurezza di rete (NSG) per Service Fabric e una subnet e un gruppo di sicurezza di rete (NSG) per Gestione API. Altre informazioni sulle reti virtuali, le subnet e NSG sono reperibili [qui](../virtual-network/virtual-networks-overview.md).

Il file dei parametri [network.parameters.json][network-parameters-arm] contiene i nomi delle subnet e dei gruppi di sicurezza di rete in cui verranno distribuiti Service Fabric e Gestione API.  Gestione API viene distribuito nell'[esercitazione seguente](service-fabric-tutorial-deploy-api-management.md). In questa guida non è necessario modificare i valori dei parametri. I modelli di Gestione risorse di Service Fabric usano questi valori.  Se i valori vengono modificati qui, è necessario modificarli negli altri modelli di Gestione risorse usati in questa esercitazione e nell'[esercitazione di distribuzione di Gestione API](service-fabric-tutorial-deploy-api-management.md). 

Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Per distribuire il modello e il file di parametri di Resource Manager per la configurazione di rete, usare il comando PowerShell seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>Creare un insieme di credenziali delle chiavi e caricare un certificato
Il modello di Resource Manager del cluster di Service Fabric descritto nel passaggio successivo è configurato per la creazione di un cluster protetto mediante un'architettura di sicurezza basata sui certificati. Il certificato viene usato per proteggere la comunicazione da nodo a nodo per il cluster e per gestire l'accesso utente al cluster di Service Fabric. Gestione API usa inoltre questo certificato per accedere al Service Fabric Naming Service per l'individuazione del servizio. È necessario disporre di un certificato nel Key Vault per la sicurezza del cluster.

Lo script seguente crea un insieme di credenziali delle chiavi in Azure e un certificato autofirmato e lo aggiunge a un insieme di credenziali delle chiavi.  Per usare un certificato esistente, impostare **$CreateSelfSignedCertificate** su "$false" e specificare il percorso in **$ExistingPfxFilePath**.

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Distribuire il cluster di Service Fabric
Una volta terminata la distribuzione delle risorse di rete e caricato il certificato nell'insieme di credenziali delle chiavi, il passaggio successivo consiste nel distribuire un cluster Service Fabric per la rete virtuale nella subnet e nel gruppo NSG designato per tale cluster. Per questa esercitazione, il modello di Resource Manager di Service Fabric è preconfigurato per l'uso dei nomi di rete virtuale, subnet e gruppo NSG impostati nel passaggio precedente.

Scaricare il modello e il file di parametri di Resource Manager seguenti:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Compilare i parametri vuoti nel file `cluster.parameters.json` per la distribuzione, incluse le [informazioni sul Key Vault](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) per il certificato del cluster.

Per distribuire il modello e i file di parametri di Resource Manager per creare il cluster di Service Fabric, usare il comando PowerShell seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>Modificare il certificato e accedere a Service Fabric Explorer 

1. Fare doppio clic sul certificato per aprire l'Importazione guidata certificati.

2. Usare le impostazioni predefinite, ma assicurarsi di spuntare la casella di testo **Mark this key as exportable.** (Contrassegnare questa chiave come esportabile.) nel passaggio **Protezione della chiave privata**. Visual Studio deve esportare il certificato durante la configurazione del Registro contenitori di Azure per l'autenticazione del cluster di Service Fabric in seguito in questa esercitazione.

3. È ora possibile aprire Service Fabric Explorer in un browser. A tale scopo, passare all'URL **ManagementEndpoint** per il cluster tramite un web browser e selezionare il certificato salvato nel computer.

>[!NOTE]
>Quando si apre Service Fabric Explorer, viene visualizzato un errore di certificato, poiché si usa un certificato autofirmato. In Edge, è necessario fare clic su *Dettagli* e quindi sul collegamento *Continua per la pagina Web*. In Chrome, è necessario fare clic su *Advanced* (Impostazioni avanzate)e quindi sul collegamento *Procedi*.

>[!NOTE]
>Se la creazione del cluster non riesce, è sempre possibile rieseguire il comando che aggiorna le risorse già distribuite. Se un certificato è stato creato come parte della distribuzione non riuscita, ne viene generato uno nuovo. Per risolvere i problemi di creazione del cluster, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro
Connettersi al cluster usando il modulo di PowerShell Service Fabric installato con Service Fabric SDK.  Installare prima di tutto il certificato nell'archivio personale (My) dell'utente corrente nel computer in uso.  Eseguire il seguente comando PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

È ora possibile connettersi al cluster sicuro.

Il modulo di PowerShell **Service Fabric** include molti cmdlet per la gestione di cluster, applicazioni e servizi di Service Fabric.  Usare il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) per connettersi al cluster sicuro. L'identificazione personale del certificato e i dettagli dell'endpoint della connessione sono disponibili nell'output di un passaggio precedente.

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

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Pulire le risorse

Un cluster è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID della sottoscrizione accedendo al [portale di Azure](http://portal.azure.com). Eliminare il gruppo di risorse e tutte le risorse del cluster con il [cmdlet Remove-AzureRmResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando un modello
> * Creare un insieme di credenziali delle chiavi e caricare un certificato
> * Creare un cluster protetto di Service Fabric in Azure usando un modello
> * Proteggere il cluster con un certificato X.509
> * Connessione al cluster mediante PowerShell
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come distribuire un'applicazione esistente.
> [!div class="nextstepaction"]
> [Distribuire Gestione API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

