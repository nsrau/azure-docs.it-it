---
title: Avvio rapido di Azure - Creare un insieme di credenziali delle chiavi e un segreto di Azure usando un modello di Azure Resource Manager | Microsoft Docs
description: Argomento di avvio rapido che illustra come creare insiemi di credenziali delle chiavi di Azure e aggiungere segreti agli insiemi di credenziali usando un modello di Azure Resource Manager.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: cff67e99d40b092341e89c96a9f1e037d8572248
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690630"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Avvio rapido: Impostare e recuperare un segreto da Azure Key Vault usando un modello di Resource Manager

[Azure Key Vault](../general/overview.md) è un servizio cloud che offre un archivio sicuro per i segreti, ad esempio le chiavi, le password, i certificati e così via. Questo argomento di avvio rapido illustra il processo di distribuzione di un modello di Azure Resource Manager per creare un insieme di credenziali delle chiavi e un segreto.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Per la configurazione delle autorizzazioni nel modello è necessario l'ID oggetto utente di Azure AD. La procedura seguente recupera l'ID oggetto (GUID).

    1. Eseguire il seguente comando di Azure PowerShell o dell'interfaccia della riga di comando di Azure selezionando **Prova** e quindi incollare lo script nel riquadro della shell. Per incollare lo script, fare clic con il pulsante destro del mouse nella shell e quindi scegliere **Incolla**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Annotare l'ID di oggetto poiché verrà richiesto nella sezione successiva di questo avvio rapido.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Nel modello sono definite due risorse di Azure:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): crea un insieme di credenziali delle chiavi di Azure.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): crea un segreto dell'insieme di credenziali delle chiavi.

Altri esempi di modelli per Azure Key Vault sono disponibili in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un insieme di credenziali delle chiavi e un segreto.

    [![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

    ![Modello di Resource Manager, integrazione di Key Vault, portale per la distribuzione](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Se non diversamente specificato, usare il valore predefinito per creare l'insieme di credenziali delle chiavi e un segreto.

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    * **Località**: selezionare una località. Ad esempio **Stati Uniti centrali**.
    * **Nome insieme di credenziali delle chiavi**: immettere un nome per l'insieme di credenziali delle chiavi. Il nome deve essere globalmente univoco all'interno dello spazio dei nomi .vault.azure.net. Sarà necessario nella sezione successiva per convalidare la distribuzione.
    * **ID tenant**: viene recuperato automaticamente dalla funzione del modello. Non modificare il valore predefinito.
    * **ID oggetto utente AD**: immettere l'ID oggetto utente di Azure AD recuperato nella sezione [Prerequisiti](#prerequisites).
    * **Nome segreto**: immettere un nome per il segreto archiviato nell'insieme di credenziali delle chiavi, ad esempio **adminpassword**.
    * **Valore del segreto**: immettere il valore del segreto. Se si archivia una password, è consigliabile usare la password generata che è stata creata nella sezione Prerequisiti.
    * **Accetto le condizioni riportate sopra**: selezionare questa casella.
3. Selezionare **Acquisto**. Al termine della distribuzione dell'insieme di credenziali delle chiavi, si riceverà una notifica:

    ![Modello di Resource Manager, integrazione di Key Vault, notifica nel portale per la distribuzione](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Per distribuire il modello, si usa il portale di Azure. Oltre al portale di Azure, è anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare il portale di Azure per controllare l'insieme di credenziali delle chiavi e il segreto oppure usare lo script dell'interfaccia della riga di comando di Azure o di Azure PowerShell seguente per elencare il segreto creato.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

L'output sarà simile al seguente:

# <a name="cli"></a>[CLI](#tab/CLI)

![Modello di Resource Manager, integrazione di Key Vault, output della convalida nel portale per la distribuzione](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Modello di Resource Manager, integrazione di Key Vault, output della convalida nel portale per la distribuzione](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni relative a Key Vault si basano su questa guida introduttiva. Se si prevede di usare le guide introduttive e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non è più necessario, eliminare il gruppo di risorse per eliminare l'istanza di Key Vault e le risorse correlate. Per eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure oppure con Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido sono stati creati un insieme di credenziali delle chiavi e un segreto usando un modello di Resource Manager ed è stata convalidata la distribuzione. Per altre informazioni su Key Vault e Azure Resource Manager, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere altre informazioni su [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
