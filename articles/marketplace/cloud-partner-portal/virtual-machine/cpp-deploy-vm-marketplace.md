---
title: Distribuire una macchina virtuale da Azure Marketplace | Microsoft Docs
description: Illustra come distribuire una macchina virtuale da una macchina virtuale preconfigurata di Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 3ec4e0b047e94dc7481c51390c6e4370fdea5efa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258801"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Distribuire una macchina virtuale da Azure Marketplace

Questo articolo illustra come distribuire una macchina virtuale (VM) preconfigurata da Azure Marketplace, usando lo script di Azure PowerShell fornito.  Questo script espone inoltre gli endpoint HTTP e HTTPS WinRM nella macchina virtuale.  Lo script richiede che si disponga già di un certificato caricato in Azure Key Vault, come descritto in [Creare certificati per Azure Key Vault](./cpp-create-key-vault-cert.md). 


## <a name="vm-deployment-template"></a>Modello di distribuzione di macchine virtuali

Il modello di distribuzione di macchine virtuali di Azure di avvio rapido è disponibile come file online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Contiene i parametri seguenti:

|  **Parametro**        |   **Descrizione**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Nome dell'account di archiviazione                       |
| dnsNameForPublicIP    | Nome DNS per l'indirizzo IP pubblico. Deve essere costituito da caratteri minuscoli.    |
| adminUserName         | Nome utente amministratore                          |
| adminPassword         | Password amministratore                          |
| imagePublisher        | Editore immagine                                   |
| imageOffer            | Offerta dell'immagine                                       |
| imageSKU              | SKU dell'immagine                                         |
| vmSize                | Dimensioni della macchina virtuale                                    |
| vmName                | Nome della VM                                    |
| vaultName             | Nome dell'insieme di credenziali delle chiavi                             |
| vaultResourceGroup    | Gruppo di risorse dell'insieme di credenziali delle chiavi                   |
| certificateUrl        | URL per il certificato, inclusa la versione in KeyVault, ad esempio `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Script di distribuzione

Modificare lo script di Azure PowerShell seguente ed eseguirlo per distribuire la macchina virtuale di Azure Marketplace specificata.

```powershell

New-AzureRmResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito una macchina virtuale preconfigurata, è possibile configurare e accedere alle soluzioni e ai servizi in essa contenuti o usarla per un ulteriore sviluppo. 
