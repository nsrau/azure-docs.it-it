---
title: Avvio rapido di Azure - Creare un modulo di protezione hardware gestito usando un modello di Azure Resource Manager
description: Questo argomento di avvio rapido illustra come creare un modulo di protezione hardware gestito di Azure Key Vault usando un modello di Resource Manager
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d47935f76347b2d5272b386942a85643a732e643
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831753"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Avvio rapido: Creare un modulo di protezione hardware gestito usando un modello di Azure Resource Manager

Il modulo di protezione hardware gestito è un servizio cloud completamente gestito, a disponibilità elevata, a tenant singolo e conforme agli standard che consente di proteggere le chiavi crittografiche per le applicazioni cloud tramite moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 3**.  

Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Resource Manager per creare un modulo di protezione hardware gestito.  Un [modello di Resource Manager](../../azure-resource-manager/templates/overview.md) è un file JSON (JavaScript Object Notation) che definisce l'infrastruttura e la configurazione del progetto. Il modello usa una sintassi dichiarativa che consente di indicare ciò che si intende distribuire senza dover scrivere la sequenza dei comandi di programmazione per crearlo. Per altre informazioni sullo sviluppo di modelli di Resource Manager, vedere la [documentazione di Resource Manager](../../azure-resource-manager/index.yml) e le [informazioni di riferimento sui modelli](/azure/templates/microsoft.keyvault/allversions).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
- Interfaccia della riga di comando di Azure versione 2.12.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure usando l'interfaccia della riga di comando è possibile digitare:

```azurecli
az login
```

Per altre informazioni sulle opzioni di accesso con l'interfaccia della riga di comando, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-manage-hsm"></a>Creare un modulo di protezione hardware gestito

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

La risorsa di Azure definita nel modello:

* **Microsoft.KeyVault/managedHSMs**: crea un modulo di protezione hardware gestito di Azure Key Vault.

Altri esempi di modelli di Azure Key Vault sono disponibili [qui](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

Il modello richiede l'ID oggetto associato all'account. Per trovarlo, usare il comando [az ad user show](/cli/azure/ad/user?view=azure-cli-latest&preserve-view=true#az_ad_user_show) dell'interfaccia della riga di comando di Azure, passando il proprio indirizzo di posta elettronica al parametro `--id`. È possibile limitare l'output solo all'ID oggetto con il parametro `--query`.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Potrebbe essere necessario anche l'ID tenant. Per trovarlo, usare il comando [az ad user show](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_show) dell'interfaccia della riga di comando di Azure. È possibile limitare l'output solo all'ID tenant con il parametro `--query`.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un insieme di credenziali delle chiavi e un segreto.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Selezionare o immettere i valori seguenti.

    A meno che non sia specificato, usare il valore predefinito per creare l'insieme di credenziali delle chiavi e un segreto.

    - **Sottoscrizione** Selezionare una sottoscrizione di Azure.
    - **Gruppo di risorse**: Selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    - **Località**: Selezionare una località. Ad esempio, **Stati Uniti centro-meridionali**.
    - **managedHSMName**: immettere un nome per il modulo di protezione hardware gestito.
    - **ID tenant**: viene recuperato automaticamente dalla funzione del modello. Non cambiare il valore predefinito.  Se non è specificato alcun valore, immettere l'ID tenant recuperato in [Prerequisiti](#prerequisites).
    * **initialAdminObjectIds**: immettere l'ID oggetto recuperato in [Prerequisiti](#prerequisites).

3. Selezionare **Acquisto**. Al termine della distribuzione dell'insieme di credenziali delle chiavi, si riceverà una notifica:

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un modulo di protezione hardware gestito. Per essere completamente funzionale, questo modulo deve essere attivato. Per informazioni su come attivarlo, vedere [Attivare il modulo di protezione hardware gestito](quick-create-cli.md#activate-your-managed-hsm).

- Leggere una [panoramica del modulo di protezione hardware gestito](overview.md)
- Informazioni sulla [gestione delle chiavi in un modulo di protezione hardware gestito](key-management.md)
- Vedere le [procedure consigliate per il modulo di protezione hardware gestito](best-practices.md)
