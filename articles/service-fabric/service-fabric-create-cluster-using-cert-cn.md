---
title: Creare un cluster di Azure Service Fabric usando il nome comune dei certificati | Microsoft Docs
description: Informazioni su come creare un cluster di Service Fabric usando il nome comune dei certificati da un modello.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: bf28ddf7facbc742a107f67f3d7e81eca5a5c950
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394269"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Distribuire un cluster di Service Fabric che usa il nome comune dei certificati anziché l'identificazione personale
Nessun certificato può avere la stessa identificazione digitale di un altro, il che rende difficile eseguire il rollover o gestire il certificato del cluster. Più certificati, tuttavia, possono avere lo stesso nome comune o lo stesso oggetto.  Un cluster che usa il nome comune del certificato rende molto più semplice la gestione certificati. In questo articolo viene descritto come distribuire un cluster di Service Fabric per usare il nome comune del certificato anziché l'identificazione personale del certificato.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Ottenere un certificato
Ottenere innanzitutto un certificato da un'[autorità di certificazione](https://wikipedia.org/wiki/Certificate_authority).  Il nome comune del certificato deve essere usato per il dominio personalizzato di cui si è proprietari e deve essere acquistato da un registrar di dominio. Ad esempio "azureservicefabricbestpractices.com"; gli utenti che non sono dipendenti Microsoft non possono effettuare il provisioning di certificati per i domini MS, pertanto non è possibile usare i nomi DNS del bilanciamento del carico o di Gestione traffico come nomi comuni per il certificato e quindi sarà necessario eseguire il provisioning di una [zona DNS di Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) affinché il dominio personalizzato sia risolvibile in Azure. È anche possibile dichiarare il dominio personalizzato di cui si è proprietari come "managementEndpoint" del cluster se si desidera che il portale rifletta l'alias di dominio personalizzato per il cluster.

A scopo di test, è possibile ottenere un certificato firmato da un'autorità di certificazione disponibile o aperta.

> [!NOTE]
> I certificati autofirmati, inclusi quelli generati quando si distribuisce un cluster di Service Fabric nel portale di Azure, non sono supportati. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Caricare il certificato in un insieme di credenziali delle chiavi
In Azure, un cluster di Service Fabric è distribuito in un set di scalabilità di macchine virtuali.  Caricare il certificato in un insieme di credenziali delle chiavi.  Quando il cluster viene distribuito, il certificato è installato nel set di scalabilità di macchine virtuali in cui è in esecuzione il cluster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Scaricare e aggiornare un modello di esempio
Questo articolo usa il modello di esempio di un [cluster protetto a 5 nodi](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) e i relativi parametri. Scaricare i file *azuredeploy.json* e *azuredeploy.parameters.json* dal computer.

### <a name="update-parameters-file"></a>Aggiornare il file dei parametri
Prima di tutto, aprire il file *azuredeploy.parameters.json* in un editor di testo e aggiungere il seguente valore di parametro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Successivamente, impostare i valori dei parametri *certificateCommonName*, *sourceVaultValue* e *certificateUrlValue* usando quelli restituiti dallo script precedente:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aggiornare il file di modello
Aprire quindi il file *azuredeploy.json* in un editor di testo e apportare tre aggiornamenti per supportare il nome comune del certificato.

1. Nella sezione **parametri**, aggiungere un parametro *certificateCommonName*:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Valutare anche la rimozione di *certificateThumbprint*, che potrebbe non essere più necessario.

2. Impostare il valore della variabile *sfrpApiVersion* in "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Nella risorsa **Microsoft.Compute/virtualMachineScaleSets**, aggiornare l'estensione macchina virtuale perché nelle impostazioni del certificato venga usato il nome comune anziché l'identificazione personale.  In **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate** aggiungere 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    e rimuovere `"thumbprint": "[parameters('certificateThumbprint')]",`.

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. Nella risorsa **Microsoft.ServiceFabric/clusters**, aggiornare la versione dell'API in "2018-02-01".  Aggiungere anche un'impostazione **certificateCommonNames** con una proprietà **commonNames** e rimuovere l'impostazione **certificato** con la proprietà identificazione personale come illustrato nell'esempio seguente:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Il campo 'certificateIssuerThumbprint' consente di specificare le autorità di certificazione previste dei certificati con un nome comune di soggetto. Questo campo accetta un'enumerazione delimitata da virgole di identificazioni personali SHA1. Si noti che in questo modo viene rafforzata la convalida del certificato. Se l'autorità di certificazione non è specificata o è vuota, il certificato viene accettato per l'autenticazione se è possibile crearne la catena e se termina in una radice considerata attendibile dal validator. Se invece l'autorità di certificazione è specificata, il certificato viene accettato nel caso in cui l'identificazione personale dell'autorità di certificazione diretta corrisponda a uno qualsiasi dei valori indicati in questo campo (indipendentemente dall'attendibilità della radice). Si noti che un'infrastruttura a chiave pubblica può usare autorità di certificazione diverse per emettere certificati per lo stesso soggetto. È quindi importante specificare tutte le identificazioni personali delle autorità di certificazione previste per un determinato soggetto.
   >
   > La procedura consigliata è quella di specificare l'autorità di certificazione. Anche se è ancora possibile ometterla senza compromettere il funzionamento (per certificati concatenati a una radice attendibile), questo comportamento è soggetto a limitazioni e, nell'immediato futuro, potrebbe essere eliminato. Si noti inoltre che i cluster distribuiti in Azure e protetti con certificati X509, emessi da un'infrastruttura a chiave pubblica privata e dichiarati dal soggetto, potrebbero non essere convalidati dal servizio Azure Service Fabric (per le comunicazioni dal cluster al servizio), se i criteri dei certificati dell'infrastruttura a chiave pubblica non sono rilevabili, disponibili e accessibili. 

## <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato
Distribuire nuovamente il modello aggiornato dopo aver apportato le modifiche.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* Informazioni su come [eseguire il rollover di un certificato di cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Aggiornare e gestire i certificati dei cluster](service-fabric-cluster-security-update-certs-azure.md)
* Semplificare la gestione dei certificati con la [modifica del cluster dall'identificazione personale del certificato al nome comune](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
ic-cluster-change-cert-thumbprint-to-cn.md))

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
