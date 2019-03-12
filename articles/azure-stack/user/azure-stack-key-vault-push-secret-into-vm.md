---
title: Distribuire una macchina virtuale con un certificato archiviato in modo sicuro in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire una macchina virtuale e un certificato su di esso push usando un insieme di credenziali delle chiavi in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 52c66dbf5001b3c49ac959276e59d8e794955193
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760122"
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Creare una macchina virtuale e installare un certificato recuperato da un insieme di credenziali delle chiavi di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come creare una macchina virtuale di Azure Stack (VM) con installato un certificato dell'insieme di credenziali chiave.

## <a name="overview"></a>Panoramica

I certificati vengono usati in molti scenari, ad esempio l'autenticazione ad Active Directory o la crittografia del traffico web. È possibile archiviare in modo sicuro i certificati come segreti in un insieme di credenziali delle chiavi di Azure Stack. I vantaggi dell'uso di Azure Stack di Key Vault sono:

* I certificati non sono esposti in uno script, della cronologia della riga di comando o modello.
* Il processo di gestione certificati è semplice.
* È necessario controllare le chiavi che accedono a certificati.

### <a name="process-description"></a>Descrizione del processo

I passaggi seguenti descrivono il processo necessario per un certificato push alla macchina virtuale:

1. Creare un insieme di credenziali di chiave privata.
2. Aggiornare il file azuredeploy.
3. Distribuire il modello.

> [!NOTE]
> È possibile usare questi passaggi da Azure Stack Development Kit o da un client esterno se si è connessi tramite VPN.

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio Key Vault.
* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).
* [Configurare l'ambiente PowerShell dell'utente di Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Creare un Key Vault secret

Lo script seguente crea un certificato in formato pfx, crea un insieme di credenziali delle chiavi e archivia il certificato nell'insieme di credenziali chiave come chiave privata.

> [!IMPORTANT]
> È necessario usare il `-EnabledForDeployment` parametro quando si crea l'insieme di credenziali delle chiavi. Questo parametro assicura che l'insieme di credenziali delle chiavi è possibile fare riferimento dai modelli di Azure Resource Manager.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

Quando si esegue lo script precedente, l'output include l'URI del segreto. Prendere nota di questo URI. È necessario farvi riferimento nel [certificato Push da modello di Resource Manager di Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Scaricare il [vm-push-certificato-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) cartella del modello nel computer di sviluppo. Questa cartella contiene il `azuredeploy.json` e `azuredeploy.parameters.json` file, che sarà necessario nei passaggi successivi.

Modificare il `azuredeploy.parameters.json` file in base ai valori di ambiente. I parametri di particolare interesse sono il nome dell'insieme di credenziali, il gruppo di risorse dell'insieme di credenziali e il segreto, URI (come generati da script precedente). La sezione seguente illustra un esempio di un file di parametri.

## <a name="update-the-azuredeployparametersjson-file"></a>Aggiornare il file azuredeploy

Aggiorna il `azuredeploy.parameters.json` file con il `vaultName`, URI del segreto, `VmName`e altri valori in base all'ambiente. Il file JSON seguente mostra un esempio del file di parametri del modello:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello usando lo script di PowerShell seguente:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando il modello viene distribuito correttamente, viene generato l'output seguente:

![Risultati della distribuzione modello](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Azure Stack inserisce il certificato alla macchina virtuale durante la distribuzione. Il percorso di certificato dipende dal sistema operativo della macchina virtuale:

* In Windows, il certificato viene aggiunto per il **LocalMachine** posizione, con l'archivio certificati dell'utente del certificato.
* In Linux, il certificato viene posizionato sotto il `/var/lib/waagent directory`, con il nome del file &lt;UppercaseThumbprint&gt;CRT per X509 file del certificato e &lt;UppercaseThumbprint&gt;.prv per la chiave privata.

## <a name="retire-certificates"></a>Ritirare i certificati

Ritiro di certificati è parte del processo di gestione di certificati. Non è possibile eliminare la versione precedente di un certificato, ma è possibile disabilitarlo usando il `Set-AzureKeyVaultSecretAttribute` cmdlet.

Nell'esempio seguente viene illustrato come disabilitare un certificato. Usare i propri valori per il **VaultName**, **Name**, e **versione** parametri.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire una VM con una password dell'insieme di credenziali delle chiavi](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Consente di accedere a Key Vault](azure-stack-key-vault-sample-app.md)
