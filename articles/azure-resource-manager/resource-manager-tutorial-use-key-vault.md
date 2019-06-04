---
title: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager | Microsoft Docs
description: Informazioni su come usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione di modelli di Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239252"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager

Questo articolo illustra come recuperare i segreti da Azure Key Vault e passarli come parametri durante la distribuzione di Resource Manager. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./resource-manager-keyvault-parameter.md).

Nell'esercitazione [Impostare l'ordine di distribuzione delle risorse](./resource-manager-tutorial-create-templates-with-dependent-resources.md) viene creata una macchina virtuale. È necessario specificare il nome utente e la password dell'amministratore della macchina virtuale. Invece di immettere la password, è possibile archiviarla preventivamente in Azure Key Vault e quindi personalizzare il modello per recuperare la password dall'insieme di credenziali delle chiavi durante la distribuzione.

![Diagramma dell'integrazione di Key Vault nei modelli di Resource Manager](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare un insieme di credenziali delle chiavi
> * Aprire un modello di avvio rapido
> * Modificare il file dei parametri
> * Distribuire il modello
> * Convalidare la distribuzione
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) con l'[estensione Strumenti di Azure Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Per una maggiore sicurezza, usare una password generata per l'account amministratore della macchina virtuale. Di seguito è riportato un esempio della generazione di una password:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Verificare che la password generata soddisfi i requisiti delle password per le macchine virtuali. Ogni servizio di Azure presenta requisiti specifici in termini di password. Per informazioni sui requisiti delle password per le VM, vedere [Quali requisiti devono avere le password durante la creazione di una VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Preparare un insieme di credenziali delle chiavi

In questa sezione si crea un insieme di credenziali delle chiavi e vi si aggiunge un segreto, per poter recuperare il segreto quando si distribuisce il modello. Esistono molti modi per creare un insieme di credenziali delle chiavi. In questa esercitazione si usa Azure PowerShell per distribuire un [modello di Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Questo modello consente di:

* Creare un insieme di credenziali delle chiavi con la proprietà `enabledForTemplateDeployment` abilitata. Per accedere ai segreti definiti nell'insieme di credenziali delle chiavi, impostare questa proprietà del processo di distribuzione di modelli su true.
* Aggiungere un segreto all'insieme di credenziali delle chiavi.  Il segreto contiene la password amministratore della macchina virtuale.

> [!NOTE]
> Se l'utente che distribuisce il modello di macchina virtuale non è il proprietario o un collaboratore dell'insieme di credenziali delle chiavi, è necessario che il proprietario o un collaboratore concedano all'utente l'accesso all'autorizzazione Microsoft.KeyVault/vaults/deploy/action per l'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./resource-manager-keyvault-parameter.md).

Per eseguire questo script di PowerShell, selezionare **Prova** per aprire Cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sul riquadro della shell e quindi scegliere **Incolla**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Alcune informazioni importanti:

* Il nome del gruppo di risorse è il nome del progetto seguito da **rg**. Per semplificare la [pulizia delle risorse create in questa esercitazione](#clean-up-resources), usare lo stesso nome di progetto e di gruppo di risorse per la [distribuzione del modello seguente](#deploy-the-template).
* Il nome predefinito del segreto è **vmAdminPassword**. È hardcoded nel modello.
* Affinché il modello possa recuperare il segreto, è necessario abilitare un criterio di accesso denominato **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli** per l'insieme di credenziali delle chiavi. Questo criterio viene abilitato nel modello. Per altre informazioni su questo criterio di accesso, vedere [Distribuire insiemi di credenziali delle chiavi e segreti](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Il modello ha un valore di output denominato **keyVaultId**. Prendere nota del valore. Questo ID sarà necessario durante la distribuzione della macchina virtuale. Il formato dell'ID risorsa è il seguente:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Quando si copia e si incolla l'ID, l'ID potrebbe essere suddiviso in più righe. È necessario unire le righe e tagliare gli spazi aggiuntivi.

Per convalidare la distribuzione, eseguire il comando di PowerShell seguente nello stesso riquadro della shell per recuperare il segreto come testo non crittografato. Il comando funziona solo nella stessa sessione della shell, perché usa una variabile $keyVaultName definita nello script di PowerShell precedente.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Ora che sono stati preparati un insieme di credenziali delle chiavi e un segreto, le sezioni seguenti illustrano come personalizzare un modello esistente per recuperare il segreto durante la distribuzione.

## <a name="open-a-quickstart-template"></a>Aprire un modello di avvio rapido

Modelli di avvio rapido di Azure è un repository di modelli di Resource Manager. Anziché creare un modello da zero, è possibile trovare un modello di esempio e personalizzarlo. Il modello usato in questa esercitazione è denominato [Distribuire una VM Windows semplice](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. In Visual Studio Code selezionare **File**>**Apri file**.
2. In **Nome file** incollare l'URL seguente:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selezionare **Apri** per aprire il file. È lo stesso scenario usato in [Esercitazione: Creare modelli di Azure Resource Manager con risorse dipendenti](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Sono presenti cinque risorse definite dal modello:

   * `Microsoft.Storage/storageAccounts`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Vedere le [informazioni di riferimento sul modello](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Prima di personalizzare il modello è utile acquisirne una conoscenza di base.
5. Selezionare **File**>**Salva con nome** per salvare una copia del file con il nome **azuredeploy.json** nel computer locale.
6. Ripetere i passaggi da 1 a 4 per aprire l'URL seguente e quindi salvare il file con il nome **azuredeploy.parameters.json**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Modificare il file dei parametri

Non è necessario apportare modifiche al file del modello.

1. Aprire **azuredeploy.parameters.json** in Visual Studio Code, se non è aperto.
2. Aggiornare il parametro **adminPassword** nel modo seguente:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Sostituire il valore di **id** con l'ID risorsa dell'insieme di credenziali delle chiavi creato nella procedura precedente.

    ![Integrare Key Vault e i modelli di Resource Manager - File dei parametri per la distribuzione di macchine virtuali](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Specificare i valori dei parametri seguenti.

    * **adminUsername**: assegnare un nome all'account amministratore della macchina virtuale.
    * **dnsLabelPrefix**: assegnare un nome al prefisso dell'etichetta DNS.

    Vedere un esempio nello screenshot precedente.

4. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, seguire le istruzioni riportate in [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). È necessario caricare sia **azuredeploy.json** che **azuredeploy.parameters.json** in Cloud Shell e quindi usare lo script di PowerShell seguente per distribuire il modello:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Quando si distribuisce il modello, usare lo stesso gruppo di risorse dell'insieme di credenziali delle chiavi. Ciò semplifica la pulizia delle risorse, perché sarà necessario eliminare un solo gruppo di risorse anziché due.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Al termine della distribuzione della macchina virtuale, testare l'accesso usando la password archiviata nell'insieme di credenziali delle chiavi.

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Selezionare **Gruppi di risorse**/**NomeGruppoRisorse>** /**simpleWinVM**
3. Selezionare **Connetti** in alto.
4. Selezionare **Scarica file RDP** e quindi seguire le istruzioni per accedere alla macchina virtuale usando la password archiviata nell'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è recuperato un segreto da Azure Key Vault e si è usato tale segreto nella distribuzione del modello.  Per informazioni su come creare modelli collegati, vedere:

> [!div class="nextstepaction"]
> [Creare modelli collegati](./resource-manager-tutorial-create-linked-templates.md)
