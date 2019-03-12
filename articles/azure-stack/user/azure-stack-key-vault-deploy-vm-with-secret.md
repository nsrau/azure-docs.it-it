---
title: Distribuire una VM con password archiviate in modo sicuro in Azure Stack | Microsoft Docs
description: Informazioni su come distribuire una macchina virtuale usando una password archiviata nell'insieme di credenziali delle chiavi di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 30ebccd6bee26900ac01f7f4d17993e24c9db745
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779250"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Creare una macchina virtuale con una password sicura archiviata nell'insieme di credenziali delle chiavi di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo illustra la distribuzione di una macchina virtuale di Windows Server usando una password archiviata nell'insieme di credenziali delle chiavi di Azure Stack. Usare una password dell'insieme di credenziali chiave è più sicura del passaggio di una password in testo normale.

## <a name="overview"></a>Panoramica

È possibile archiviare i valori, ad esempio una password come segreto in un insieme di credenziali delle chiavi di Azure Stack. Dopo aver creato una chiave privata, è possibile farvi riferimento nei modelli di Azure Resource Manager. Uso dei segreti con Resource Manager offre i vantaggi seguenti:

* Non è necessario immettere manualmente segreto ogni volta che si distribuisce una risorsa.
* È possibile specificare quali utenti o entità servizio possono accedere a un segreto.

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio Key Vault.
* [Installare PowerShell per Azure Stack.](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell.](azure-stack-powershell-configure-user.md)

I passaggi seguenti descrivono il processo necessario per creare una macchina virtuale per il recupero della password archiviata in un insieme di credenziali delle chiavi:

1. Creare un insieme di credenziali di chiave privata.
2. Aggiornare il file azuredeploy.
3. Distribuire il modello.

> [!NOTE]  
> È possibile usare questi passaggi da Azure Stack Development Kit o da un client esterno se si è connessi tramite VPN.

## <a name="create-a-key-vault-secret"></a>Creare un Key Vault secret

Lo script seguente crea un insieme di credenziali delle chiavi e archivia una password nell'insieme di credenziali chiave come chiave privata. Usare il `-EnabledForDeployment` parametro quando si crea l'insieme di credenziali delle chiavi. Questo parametro assicura che l'insieme di credenziali delle chiavi è possibile fare riferimento dai modelli di Azure Resource Manager.

```PowerShell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Quando si esegue lo script precedente, l'output include l'URI del segreto. Prendere nota di questo URI. È necessario farvi riferimento nel [macchina virtuale di distribuzione Windows con password nel modello di insieme di credenziali delle chiavi](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Scaricare il [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) cartella nel computer di sviluppo. Questa cartella contiene il `azuredeploy.json` e `azuredeploy.parameters.json` file, che sarà necessario nei passaggi successivi.

Modificare il `azuredeploy.parameters.json` file in base ai valori di ambiente. I parametri di particolare interesse sono il nome dell'insieme di credenziali, il gruppo di risorse dell'insieme di credenziali e il segreto, URI (come generati da script precedente). Il file seguente è un esempio di un file di parametri:

## <a name="update-the-azuredeployparametersjson-file"></a>Aggiornare il file azuredeploy

Aggiornare il file azuredeploy con l'URI di KeyVault, secretName, adminUsername dei valori di macchina virtuale in base all'ambiente. Il file JSON seguente mostra un esempio del file di parametri del modello:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Distribuzione del modello

A questo punto è possibile distribuire il modello usando lo script di PowerShell seguente:

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando il modello viene distribuito correttamente, viene generato l'output seguente:

![Output di distribuzione](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'app di esempio con Key Vault](azure-stack-key-vault-sample-app.md)
* [Distribuire una macchina virtuale con un certificato di Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
