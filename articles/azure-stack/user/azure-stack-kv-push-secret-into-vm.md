---
title: Distribuire una macchina virtuale con un certificato archiviato in modo sicuro nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire una macchina virtuale e push di un certificato su di esso con un insieme di credenziali chiave nello Stack di Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 3950c9dfc5ff5f7ea1d170da086b4f97048ed81c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-and-install-a-certificate-retrieved-from-an-azure-stack-key-vault"></a>Creare una macchina virtuale e installare un certificato recuperato da un insieme di credenziali delle chiavi di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Informazioni su come creare una macchina virtuale di Azure Stack (VM) con installato un certificato di chiave dell'insieme di credenziali.

## <a name="overview"></a>Panoramica

I certificati vengono usati in molti scenari, ad esempio l'autenticazione ad Active Directory o la crittografia del traffico web. È possibile archiviare in modo sicuro i certificati come segreti in un insieme di credenziali delle chiavi di Azure Stack. I vantaggi dell'utilizzo dell'insieme di credenziali di Azure Stack chiave sono:

* Certificati non sono esposti in uno script, della cronologia della riga di comando o modello.
* Il processo di gestione certificati è semplice.
* Hai un controllo delle chiavi che i certificati di accesso.

### <a name="process-description"></a>Descrizione del processo

I passaggi seguenti descrivono il processo necessario effettuare il push di un certificato nella macchina virtuale:

1. Creare un insieme di credenziali chiave segreta.
2. Aggiornare il file azuredeploy.parameters.json.
3. Distribuire il modello

>[!NOTE]
>Se si è connessi tramite VPN, è possibile utilizzare questi passaggi dal Kit di sviluppo dello Stack di Azure o da un client esterno.

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio insieme di credenziali chiave.
* [Installare PowerShell per Azure dello Stack.](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)

## <a name="create-a-key-vault-secret"></a>Creare un insieme di credenziali chiave segreta

Lo script seguente crea un certificato in formato pfx, crea un insieme di credenziali chiave e archivia il certificato nell'insieme di credenziali chiave come chiave privata.

>[!IMPORTANT]
>È necessario utilizzare il `-EnabledForDeployment` parametro quando si crea l'errore di chiave. Questo parametro assicura che l'insieme di credenziali delle chiavi può far riferimento dai modelli di gestione risorse di Azure.

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

Distribuire il modello tramite lo script PowerShell seguente:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando il modello viene distribuito correttamente, otterrà l'output seguente:

![Risultati della distribuzione modello](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Stack Azure inserisce il certificato nella macchina virtuale durante la distribuzione. Percorso del certificato dipende dal sistema operativo della macchina virtuale:

* In Windows, il certificato viene aggiunto nel percorso certificato LocalMachine, con l'archivio certificati fornito dall'utente.
* In Linux, il certificato viene inserito nella directory /var/lib/waagent, con il nome del file &lt;UppercaseThumbprint&gt;CRT per X509 file del certificato e &lt;UppercaseThumbprint&gt;.prv per la chiave privata .

## <a name="retire-certificates"></a>Ritirare i certificati

Ritiro di certificati fa parte del processo di gestione del certificato. Non è possibile eliminare la versione precedente di un certificato, ma è possibile disabilitarlo utilizzando il `Set-AzureKeyVaultSecretAttribute` cmdlet.

Nell'esempio seguente viene illustrato come disabilitare un certificato. Usare i propri valori per il **VaultName**, **nome**, e **versione** parametri.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire una VM con una password dell'insieme di credenziali delle chiavi](azure-stack-kv-deploy-vm-with-secret.md)
* [Consentire a un'applicazione per l'insieme di credenziali chiave di accesso](azure-stack-kv-sample-app.md)
