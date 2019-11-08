---
title: Distribuire una VM da Azure Marketplace
description: Viene illustrato come distribuire una macchina virtuale da una macchina virtuale preconfigurata di Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 2888d7643fd4f624634dc2ec520bec6e753382f1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816833"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Distribuire una macchina virtuale da Azure Marketplace

Questo articolo illustra come distribuire una macchina virtuale (VM) preconfigurata da Azure Marketplace, usando lo script di Azure PowerShell fornito.  Questo script espone inoltre gli endpoint HTTP e HTTPS WinRM nella macchina virtuale.  Lo script richiede che si disponga già di un certificato caricato in Azure Key Vault, come descritto in [Creare certificati per Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

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

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito una macchina virtuale preconfigurata, è possibile configurare e accedere alle soluzioni e ai servizi in essa contenuti o usarla per un ulteriore sviluppo. 
