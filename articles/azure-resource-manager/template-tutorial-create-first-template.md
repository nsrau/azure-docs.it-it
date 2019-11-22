---
title: 'Esercitazione: Creare e distribuire un modello di Azure Resource Manager'
description: Creare il primo modello di Azure Resource Manager. L'esercitazione illustra la sintassi del file del modello e spiega come distribuire un account di archiviazione.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 463f9a54648a9e6950f839bd856ccf42bc66dca0
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074363"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Esercitazione: Creare e distribuire il primo modello di Azure Resource Manager

Questa esercitazione presenta i modelli di Azure Resource Manager e illustra come creare un modello di base e distribuirlo in Azure. Verrà descritta la struttura del modello e verranno fornite informazioni sugli strumenti necessari per l'utilizzo dei modelli. Per completare l'esercitazione sono necessari circa **12 minuti**, ma il tempo effettivo può variare in base al numero di strumenti da installare.

Questa è la prima esercitazione di una serie. Nelle esercitazioni successive della serie si modificherà il modello di base passo dopo passo fino a quando non sono state esaminate tutti gli elementi fondamentali di un modello di Resource Manager. Questi elementi costituiscono i blocchi predefiniti di modelli molto più complessi. Entro la fine della serie si dovrebbe essere in grado di creare modelli personalizzati, già pronti per automatizzare le distribuzioni con i modelli.

Per informazioni sui vantaggi derivanti dall'uso dei modelli e sul motivo per cui è consigliabile automatizzare la distribuzione con i modelli, vedere [Modelli di Azure Resource Manager](template-deployment-overview.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="get-tools"></a>Ottenere gli strumenti

Per iniziare, verificare di disporre degli strumenti necessari per creare e distribuire i modelli.

### <a name="editor"></a>Editor

I modelli sono file JSON. Per creare i modelli, è necessario un editor JSON valido. È consigliabile usare Visual Studio Code con l'estensione Strumenti di Resource Manager. Se è necessario installare questi strumenti, vedere [Usare Visual Studio Code per creare modelli di Azure Resource Manager](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Distribuzione dalla riga di comando

Per distribuire il modello, è necessario usare anche l'interfaccia della riga di comando di Azure oppure Azure PowerShell. Per le istruzioni di installazione, vedere:

- [Installare Azure PowerShell](/powershell/azure/install-az-ps)
- [Installare l'interfaccia della riga di comando di Azure in Windows](/cli/azure/install-azure-cli-windows)
- [Installare l'interfaccia della riga di comando di Azure in Linux](/cli/azure/install-azure-cli-linux)

Dopo aver installato Azure PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di accedere per la prima volta. Per informazioni, vedere [Accesso - PowerShell](/powershell/azure/install-az-ps#sign-in) or [Accesso - Interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli#sign-in).

A questo punto è possibile iniziare a familiarizzare con i modelli.

## <a name="create-your-first-template"></a>Creare il primo modello

1. Aprire Visual Studio Code con l'estensione Strumenti di Resource Manager installata.
1. Nel menu **File** selezionare **Nuovo file** per creare un nuovo file.
1. Nel menu **File** selezionare **Salva con nome**.
1. Assegnare al file il nome **azuredeploy** e selezionare l'estensione di file **JSON**. Il nome completo del file è **azuredeploy.json**.
1. Salvare il file nella workstation. Selezionare un percorso facile da ricordare perché sarà necessario specificare tale percorso in un secondo momento durante la distribuzione del modello.
1. Copiare e incollare il codice JSON seguente nel file:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Ecco come appare l'ambiente VS Code:

    ![Modello di Resource Manager in Visual Studio Code: primo modello](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Questo modello non distribuisce alcuna risorsa. Si inizierà con un modello vuoto, in modo da acquisire familiarità con i passaggi necessari per distribuire un modello riducendo al minimo le probabilità che si verifichi un errore.

    Il file JSON è costituito dagli elementi seguenti:

    - **$schema**: specifica il percorso del file di schema JSON. Il file di schema descrive le proprietà disponibili all'interno di un modello. Ad esempio, lo schema definisce **resources** come una delle proprietà valide per un modello. Non preoccuparsi che la data dello schema sia 2015-01-01. Questa versione dello schema è aggiornata e include tutte le funzionalità più recenti. La data dello schema non è stata modificata perché dalla sua introduzione non sono state apportate modifiche sostanziali.
    - **contentVersion**: specifica la versione del modello, ad esempio 1.0.0.0. Questo elemento accetta tutti i valori. Usare questo valore per documentare le modifiche significative al modello. Quando si distribuiscono risorse tramite il modello, è possibile usare questo valore per assicurarsi che venga usato il modello corretto.
    - **resources**: contiene le risorse da distribuire o aggiornare. Attualmente è vuoto, ma verranno aggiunte risorse in un secondo momento.

1. Salvare il file.

Il primo modello è stato creato.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per iniziare a usare Azure PowerShell o l'interfaccia della riga di comando di Azure, accedere con le credenziali di Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Creare un gruppo di risorse

Quando si distribuisce un modello, si specifica un gruppo di risorse che conterrà le risorse. Prima di eseguire il comando di distribuzione, creare il gruppo di risorse usando l'interfaccia della riga di comando di Azure oppure Azure PowerShell. Selezionare le schede nella sezione di codice seguente per scegliere tra Azure PowerShell e l'interfaccia della riga di comando di Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire il modello, usare l'interfaccia della riga di comando di Azure o Azure PowerShell. Usare il gruppo di risorse creato. Assegnare un nome alla distribuzione in modo da poterla identificare facilmente nella cronologia di distribuzione. Per praticità, creare anche una variabile che archivia il percorso del file modello. Questa variabile semplifica l'esecuzione dei comandi di distribuzione perché non è necessario digitare nuovamente il percorso a ogni nuova distribuzione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Il comando di distribuzione restituisce risultati. Cercare `ProvisioningState` per verificare se la distribuzione è riuscita.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![Stato del provisioning della distribuzione con PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

![Stato del provisioning della distribuzione con l'interfaccia della riga di comando di Azure](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu di sinistra selezionare **Gruppi di risorse**.

1. Selezionare il gruppo di risorse distribuito nell'ultima procedura. Il nome predefinito è **myResourceGroup**. Si noterà che nel gruppo di risorse non ci sono risorse distribuite.

1. Notare che in alto a destra nella panoramica viene visualizzato lo stato della distribuzione. Selezionare **1 riuscita**.

   ![Visualizzare lo stato della distribuzione](./media/template-tutorial-create-first-template/deployment-status.png)

1. Viene visualizzata una cronologia delle distribuzioni per il gruppo di risorse. Selezionare **blanktemplate**.

   ![Selezionare la distribuzione](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Viene visualizzato un riepilogo della distribuzione. In questo caso, i dati visualizzati sono minimi perché non sono state distribuite risorse. Più avanti in questa serie può essere utile esaminare il riepilogo nella cronologia di distribuzione. Si noti che a sinistra è possibile visualizzare gli input, gli output e il modello usato durante la distribuzione.

   ![Visualizzare il riepilogo della distribuzione](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

È stato creato un modello semplice per la distribuzione in Azure. Nell'esercitazione successiva si aggiungerà un account di archiviazione al modello per distribuirlo nel gruppo di risorse.

> [!div class="nextstepaction"]
> [Aggiungere la risorsa](template-tutorial-add-resource.md)
