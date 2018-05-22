---
title: Distribuire una macchina virtuale con password archiviate in modo sicuro nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire una macchina virtuale usando una password archiviate nell'insieme di credenziali chiave di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: 4239eb31afd4abc8b3555f0ee353f5d96716d623
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Creare una macchina virtuale usando una password sicura archiviata nell'insieme di credenziali chiave di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

In questo articolo esaminato distribuisce una macchina virtuale di Windows Server utilizzando una password archiviate nell'insieme di credenziali chiave di Azure dello Stack. Utilizzando la password dell'insieme di credenziali chiave è più sicura del passaggio di una password in testo normale.

## <a name="overview"></a>Panoramica

È possibile archiviare i valori, ad esempio una password come una chiave privata in un insieme di credenziali delle chiavi di Azure Stack. Dopo aver creato una chiave privata, è possibile farvi riferimento nei modelli di gestione risorse di Azure. Utilizzo di segreti con Gestione risorse offre i vantaggi seguenti:

* Non è necessario immettere manualmente segreto ogni volta che si distribuisce una risorsa.
* È possibile specificare quali utenti o le entità servizio possono accedere a una chiave privata.

## <a name="prerequisites"></a>Prerequisiti

* È necessario sottoscrivere un'offerta che include il servizio insieme di credenziali chiave.
* [Installare PowerShell per Azure dello Stack.](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell di Azure Stack dell'utente.](azure-stack-powershell-configure-user.md)

I passaggi seguenti descrivono il processo necessario per creare una macchina virtuale per il recupero della password archiviata in un insieme di credenziali chiave:

1. Creare un insieme di credenziali chiave segreta.
2. Aggiornare il file azuredeploy.parameters.json.
3. Distribuire il modello.

>[NOTA] Se si è connessi tramite VPN, è possibile utilizzare questi passaggi dal Kit di sviluppo dello Stack di Azure o da un client esterno.

## <a name="create-a-key-vault-secret"></a>Creare un insieme di credenziali chiave segreta

Lo script seguente crea un insieme di credenziali chiave e archivia una password nell'insieme di credenziali chiave come chiave privata. Utilizzare il `-EnabledForDeployment` parametro quando si crea l'insieme di credenziali chiave. Questo parametro assicura che l'insieme di credenziali chiave possono far riferimento dai modelli di gestione risorse di Azure.

```powershell

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

Quando si esegue lo script precedente, l'output include l'URI segreta. Prendere nota dell'URI. È necessario farvi riferimento nel [macchina virtuale di distribuzione Windows con password nel modello di insieme di credenziali chiave](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Scaricare il [101-vm--password di protezione](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) cartella sul computer di sviluppo. Questa cartella contiene il `azuredeploy.json` e `azuredeploy.parameters.json` file, sarà necessario nei passaggi successivi.

Modificare il `azuredeploy.parameters.json` file in base ai valori ambiente. I parametri di particolare interesse sono il nome dell'insieme di credenziali, il gruppo di risorse dell'insieme di credenziali e il segreto URI (generati da script precedente). Il file seguente è riportato un esempio di un file di parametro:

## <a name="update-the-azuredeployparametersjson-file"></a>Aggiornare il file azuredeploy.parameters.json

Aggiornare il file azuredeploy.parameters.json con l'URI KeyVault, secretName, adminUsername dei valori di macchina virtuale in base al proprio ambiente. Il file JSON seguente mostra un esempio di file dei parametri di modello:

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Ora è possibile distribuire il modello utilizzando lo script di PowerShell seguente:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Quando il modello viene distribuito correttamente, otterrà l'output seguente:

![Output di distribuzione](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un'app di esempio con l'insieme di credenziali chiave](azure-stack-kv-sample-app.md)

[Distribuire una macchina virtuale con un certificato di chiave dell'insieme di credenziali](azure-stack-kv-push-secret-into-vm.md)
