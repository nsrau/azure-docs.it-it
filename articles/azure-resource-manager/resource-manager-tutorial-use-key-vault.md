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
ms.date: 01/25/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0e73177ca49a9a100b45712833b1310d54852680
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498013"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Esercitazione: Integrare Azure Key Vault nella distribuzione di modelli di Resource Manager

Questo articolo illustra come recuperare i segreti da Azure Key Vault e passarli come parametri durante la distribuzione di Resource Manager. Il valore non viene mai esposto, in quanto si fa riferimento solo all'ID dell'insieme di credenziali chiave. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./resource-manager-keyvault-parameter.md).

Nell'esercitazione [Impostare l'ordine di distribuzione delle risorse](./resource-manager-tutorial-create-templates-with-dependent-resources.md) si creano una macchina virtuale, una rete virtuale e alcune altre risorse dipendenti. In questa esercitazione si personalizza il modello per recuperare la password amministratore della macchina virtuale da un insieme di credenziali delle chiavi.

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

In questa sezione si userà un modello di Resource Manager per creare un insieme di credenziali delle chiavi e un segreto. Questo modello consente di:

* Creare un insieme di credenziali delle chiavi con la proprietà `enabledForTemplateDeployment` abilitata. Per accedere ai segreti definiti nell'insieme di credenziali delle chiavi, impostare questa proprietà del processo di distribuzione di modelli su true.
* Aggiungere un segreto all'insieme di credenziali delle chiavi.  Il segreto contiene la password amministratore della macchina virtuale.

Se l'utente che distribuisce il modello di macchina virtuale non è il proprietario o un collaboratore dell'insieme di credenziali delle chiavi, è necessario che il proprietario o un collaboratore concedano all'utente l'accesso all'autorizzazione Microsoft.KeyVault/vaults/deploy/action per l'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](./resource-manager-keyvault-parameter.md).

Per la configurazione delle autorizzazioni nel modello è necessario l'ID oggetto utente di Azure AD. La procedura seguente recupera l'ID oggetto (GUID).

1. Eseguire il comando seguente di Azure PowerShell o dell'interfaccia della riga di comando di Azure.  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzADUser -UserPrincipalName $upn).Id
    ```
2. Annotare l'ID di oggetto perché sarà necessario più avanti in questa esercitazione.

Per creare un insieme di credenziali delle chiavi:

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un insieme di credenziali delle chiavi e un segreto.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selezionare o immettere i valori seguenti.  Non selezionare **Acquista** al termine dell'immissione dei valori.

    ![Integrazione di Key Vault nei modelli di Resource Manager - Portale per la distribuzione](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: assegnare un nome univoco. Annotare questo nome, perché lo stesso gruppo di risorse verrà usato per distribuire la macchina virtuale nella sessione successiva. L'inclusione dell'insieme di credenziali delle chiavi e della macchina virtuale nello stesso gruppo di risorse semplifica la pulizia delle risorse alla fine dell'esercitazione.
    * **Località**: selezionare una località.  La località predefinita è **Stati Uniti centrali**.
    * **Nome insieme di credenziali delle chiavi**: assegnare un nome univoco. 
    * **ID tenant**: viene recuperato automaticamente dalla funzione del modello.  Non modificare il valore predefinito.
    * **Ad User Id** (ID utente AD): immettere l'ID oggetto utente di Azure AD recuperato con la procedura precedente.
    * **Nome segreto**: il nome predefinito è **vmAdminPassword**. Se si modifica il nome del segreto in questa posizione, è necessario aggiornarlo quando si distribuisce la macchina virtuale.
    * **Valore del segreto**: immettere il segreto.  Il segreto è la password usata per accedere alla macchina virtuale. È consigliabile usare la password generata che è stata creata nella procedura precedente.
    * **Accetto le condizioni riportate sopra**: selezionare questa casella.
3. Selezionare **Modifica parametri** in alto per esaminare il modello.
4. Passare alla riga 28 del file JSON del modello, corrispondente alla definizione dell'insieme di credenziali delle chiavi.
5. Passare alla riga 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` è una proprietà di Key Vault. Per poter recuperare i segreti dall'insieme di credenziali delle chiavi durante la distribuzione, è necessario che questa proprietà sia impostata su true.
6. Passare alla riga 89, corrispondente alla definizione del segreto di Key Vault.
7. Fare clic su **Annulla** nella parte inferiore della pagina. Non è stata apportata alcuna modifica.
8. Verificare di aver specificato tutti i valori come illustrato nello screenshot precedente e quindi fare clic su **Acquista** nella parte inferiore della pagina.
9. Selezionare l'icona a forma di campana delle notifiche nella parte superiore della pagina per aprire il riquadro **Notifiche**. Attendere il completamento della distribuzione della risorsa.
10. Selezionare **Vai al gruppo di risorse** nel riquadro **Notifiche**. 
11. Selezionare il nome dell'insieme di credenziali delle chiavi per aprirlo.
12. Selezionare **Segreti** nel riquadro sinistro. **vmAdminPassword** deve essere presente.
13. Selezionare **Criteri di accesso** nel riquadro sinistro. Il nome dell'utente (di Active Directory) dovrà essere incluso nell'elenco. In caso contrario, non si hanno le autorizzazioni necessarie per accedere all'istanza di Key Vault.
14. Selezionare **Fare clic per visualizzare i criteri di accesso avanzati**. Si noti che l'opzione **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli** è selezionata. Questa impostazione è un'altra condizione per il funzionamento dell'integrazione di Key Vault.

    ![Integrazione di Key Vault nei modelli di Resource Manager - Criteri di accesso](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
15. Selezionare **Proprietà** nel riquadro sinistro.
16. Copiare il valore di **ID risorsa**. Questo ID sarà necessario durante la distribuzione della macchina virtuale.  Il formato dell'ID risorsa è il seguente:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

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
    Sostituire **id** con l'ID risorsa dell'insieme di credenziali delle chiavi creato nella procedura precedente.  

    ![Integrare Key Vault e i modelli di Resource Manager - File dei parametri per la distribuzione di macchine virtuali](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Specificare i valori dei parametri seguenti.

    * **adminUsername**: assegnare un nome all'account amministratore della macchina virtuale.
    * **dnsLabelPrefix**: assegnare un nome al prefisso dell'etichetta DNS.
4. Salvare le modifiche.

## <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, seguire le istruzioni riportate in [Distribuire il modello](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). È necessario caricare sia **azuredeploy.json** che **azuredeploy.parameters.json** in Cloud Shell e quindi usare lo script di PowerShell seguente per distribuire il modello:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Quando si distribuisce il modello, usare lo stesso gruppo di risorse dell'insieme di credenziali delle chiavi. Ciò semplifica la pulizia delle risorse, perché sarà necessario eliminare un solo gruppo di risorse anziché due.

## <a name="valid-the-deployment"></a>Convalidare la distribuzione

Al termine della distribuzione della macchina virtuale, testare l'accesso usando la password archiviata nell'insieme di credenziali delle chiavi.

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Selezionare **Gruppi di risorse**/**NomeGruppoRisorse>**/**simpleWinVM**
3. Selezionare **Connetti** in alto.
4. Selezionare **Scarica file RDP** e quindi seguire le istruzioni per accedere alla macchina virtuale usando la password archiviata nell'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.  Nel gruppo di risorse verranno visualizzate in totale sei risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è recuperato un segreto da Azure Key Vault e si è usato tale segreto nella distribuzione del modello.  Per informazioni su come creare modelli collegati, vedere:

> [!div class="nextstepaction"]
> [Creare modelli collegati](./resource-manager-tutorial-create-linked-templates.md)
