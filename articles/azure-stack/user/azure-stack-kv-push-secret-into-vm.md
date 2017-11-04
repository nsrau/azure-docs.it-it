---
title: Distribuire una macchina virtuale con un certificato archiviato in modo sicuro nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire una macchina virtuale e push di un certificato su di esso con un insieme di credenziali chiave nello Stack di Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Creare una macchina virtuale e includere certificato recuperato da un insieme di credenziali chiave

In questo articolo consente di creare una macchina virtuale nello Stack di Azure e i certificati di push su di esso. 

## <a name="prerequisites"></a>Prerequisiti

* È necessario deve sottoscrivere un'offerta che include il servizio insieme di credenziali chiave. 
* [Installare PowerShell per Azure dello Stack.](azure-stack-powershell-install.md)  
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)

Un insieme di credenziali chiave nello Stack di Azure viene utilizzato per archiviare i certificati. I certificati sono utili in molti scenari diversi. Ad esempio, si consideri uno scenario in cui si dispone di una macchina virtuale nello Stack di Azure che esegue un'applicazione che richiede un certificato. Questo certificato può essere utilizzato per la crittografia, per l'autenticazione ad Active Directory o per SSL in un sito Web. Con il certificato nell'insieme di credenziali chiave utili assicurarsi che sia sicuro.

In questo articolo sono illustrati i passaggi necessari per eseguire il push nello Stack di Azure un certificato in una macchina virtuale di Windows. Se si è connessi tramite VPN, è possibile utilizzare questi passaggi del Kit di sviluppo dello Stack di Azure o da un client esterno basato su Windows.

I passaggi seguenti descrivono il processo necessario effettuare il push di un certificato nella macchina virtuale:

1. Creare un insieme di credenziali chiave segreta.
2. Aggiornare il file azuredeploy.parameters.json.
3. Distribuire il modello

## <a name="create-a-key-vault-secret"></a>Creare un insieme di credenziali chiave segreta

Lo script seguente crea un certificato in formato pfx, crea un insieme di credenziali chiave e archivia il certificato nell'insieme di credenziali chiave come chiave privata. È necessario utilizzare il `-EnabledForDeployment` parametro quando si crea l'insieme di credenziali chiave. Questo parametro assicura che l'insieme di credenziali chiave possono far riferimento dai modelli di gestione risorse di Azure.

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

Quando si esegue lo script precedente, l'output include l'URI segreta. Prendere nota dell'URI. È necessario farvi riferimento nel [: certificato Push al modello di gestione risorse di Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Scaricare il [modello di macchina virtuale-push-certificato windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) cartella sul computer di sviluppo. Questa cartella contiene il `azuredeploy.json` e `azuredeploy.parameters.json` file, sarà necessario nei passaggi successivi.

Modificare il `azuredeploy.parameters.json` file in base ai valori ambiente. I parametri di particolare interesse sono il nome dell'insieme di credenziali, il gruppo di risorse dell'insieme di credenziali e il segreto URI (generati da script precedente). Il file seguente è riportato un esempio di un file di parametro:

## <a name="update-the-azuredeployparametersjson-file"></a>Aggiornare il file azuredeploy.parameters.json

Aggiornare il file azuredeploy.parameters.json con il vaultName, URI segreta, VmName e altri valori in base al proprio ambiente. Il file JSON seguente mostra un esempio di file dei parametri di modello: 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Ora è possibile distribuire il modello utilizzando lo script di PowerShell seguente:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando il modello viene distribuito correttamente, otterrà l'output seguente:

![Output di distribuzione](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Quando viene distribuita la macchina virtuale, Azure Stack inserisce il certificato nella macchina virtuale. In Windows, il certificato viene aggiunto nel percorso certificato LocalMachine, con l'archivio certificati fornito dall'utente. In Linux, il certificato viene inserito nella directory /var/lib/waagent, con il nome del file &lt;UppercaseThumbprint&gt;CRT per X509 file del certificato e &lt;UppercaseThumbprint&gt;.prv per la chiave privata .

## <a name="retire-certificates"></a>Ritirare i certificati

Nella sezione precedente, è stato illustrato come inserire un nuovo certificato in una macchina virtuale. Il certificato precedente è ancora nella macchina virtuale e non può essere rimosso. Tuttavia, è possibile disabilitare la versione del segreto precedente tramite il `Set-AzureKeyVaultSecretAttribute` cmdlet. Di seguito è riportato un esempio di utilizzo di questo cmdlet. Assicurarsi di sostituire il nome dell'insieme di credenziali, nome segreto e valori di versione in base al proprio ambiente:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire una VM con una password dell'insieme di credenziali delle chiavi](azure-stack-kv-deploy-vm-with-secret.md)
* [Consentire a un'applicazione per l'insieme di credenziali chiave di accesso](azure-stack-kv-sample-app.md)


