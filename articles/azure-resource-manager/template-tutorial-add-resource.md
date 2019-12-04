---
title: 'Esercitazione: Aggiungere una risorsa al modello'
description: Descrive i passaggi per creare il primo modello di Azure Resource Manager. Viene illustrata la sintassi dei file modello e viene spiegato su come distribuire un account di archiviazione.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 924c369465bf53ea5f58de906bd0894ce822cac3
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74148187"
---
# <a name="tutorial-add-a-resource-to-your-resource-manager-template"></a>Esercitazione: Aggiungere una risorsa al modello di Azure Resource Manager

Nell'[esercitazione precedente](template-tutorial-create-first-template.md) è stato illustrato come creare un modello vuoto e distribuirlo. A questo punto, è possibile distribuire una risorsa effettiva. In questa esercitazione viene aggiunto un account di archiviazione. Per completare l'esercitazione, sono necessari circa **9 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione introduttiva sui modelli](template-tutorial-create-first-template.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Aggiungere una risorsa

Per aggiungere una definizione di account di archiviazione al modello esistente, esaminare il codice JSON evidenziato nell'esempio seguente. Invece di provare a copiare sezioni del modello, copiare l'intero file e sostituire il modello con il relativo contenuto.

Sostituire **{provide-unique-name}** con un nome di account di archiviazione univoco. Il nome dell'account di archiviazione deve essere univoco in Azure. Il nome deve essere composto solo da lettere minuscole e numeri e non deve superare i 24 caratteri. Come criterio di denominazione è possibile provare a usare il prefisso **store1** e aggiungere le proprie iniziali e la data odierna. Il nome usato può, ad esempio, essere simile a **store1abc09092019**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json?range=1-19&highlight=5-17)]

Formulare un nome univoco per un account di archiviazione non è semplice e non funziona correttamente per l'automazione di distribuzioni di grandi dimensioni. Più avanti in questa serie di esercitazioni si useranno le funzionalità del modello per semplificare la creazione di un nome univoco.

## <a name="resource-properties"></a>Proprietà risorsa

Per trovare le proprietà da usare per ogni tipo di risorsa, è possibile usare le [informazioni di riferimento per i modelli di Resource Manager](/azure/templates/) che consentono di individuare i tipi di risorsa da distribuire.

A ogni risorsa distribuita sono assegnate almeno le tre proprietà seguenti:

- **type**: Tipo di risorsa. Questo valore è una combinazione dello spazio dei nomi del provider di risorse e del tipo di risorsa, ad esempio Microsoft.Storage/storageAccounts.
- **apiVersion**: Versione dell'API REST da utilizzare per la creazione della risorsa. Ogni provider di risorse ha pubblicato le proprie versioni API, di conseguenza questo valore è specifico del tipo.
- **name**: Nome della risorsa.

Alla maggior parte delle risorse è assegnata anche una proprietà **location**, che consente di impostare l'area in cui viene distribuita la risorsa.

Le altre proprietà variano in base al tipo di risorsa e alla versione dell'API. Dal momento che è importante comprendere la connessione tra la versione dell'API e le proprietà disponibili, verranno approfonditi questi aspetti.

In questa esercitazione è stato aggiunto un account di archiviazione al modello. La versione dell'API è disponibile in [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Si noti che non tutte le proprietà sono state aggiunte al modello. Molte proprietà sono infatti facoltative. Il provider di risorse Microsoft.Storage potrebbe rilasciare una nuova versione dell'API, ma non è necessario modificare la versione distribuita. È possibile continuare a usare tale versione ed essere certi che i risultati della distribuzione saranno comunque coerenti.

Se si visualizza una versione precedente dell'API, ad esempio [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), si noterà che è disponibile un set di proprietà di dimensioni inferiori.

Se si decide di modificare la versione dell'API per una risorsa, assicurarsi di valutare le proprietà della versione e modificare il modello di conseguenza.

## <a name="deploy-template"></a>Distribuire il modello

È possibile distribuire il modello per creare l'account di archiviazione. Assegnare alla distribuzione un nome diverso in modo da poterla trovare facilmente nella cronologia.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Potrebbero verificarsi due errori di distribuzione:

- Errore: Code=AccountNameInvalid; Message={provide-unique-name} non è un nome valido per l'account di archiviazione. I nomi degli account di archiviazione devono essere compresi tra 3 e 24 caratteri e usare solo numeri e lettere minuscole.

    Nel modello sostituire **{provide-unique-name}** con un nome di account di archiviazione univoco.  Vedere [Aggiungere una risorsa](#add-resource).

- Errore: Code=StorageAccountAlreadyTaken; Message=L'account di archiviazione denominato store1abc09092019 è già assegnato.

    Nel modello provare con un nome di account di archiviazione diverso.

Questa distribuzione richiede più tempo rispetto alla distribuzione del modello vuoto perché viene creato l'account di archiviazione. L'operazione può richiedere circa un minuto, ma è in genere più veloce.

## <a name="verify-deployment"></a>Verificare la distribuzione

Per verificare la distribuzione, esplorare il gruppo di risorse nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu di sinistra selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse in cui è stata eseguita la distribuzione.
1. Si noterà che è stato distribuito un account di archiviazione.
1. Notare che l'etichetta di distribuzione ora è: **Distribuzioni: 2 riuscite**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

È stato creato un modello semplice per distribuire un account di archiviazione di Azure.  Nelle esercitazioni successive verrà illustrato come aggiungere parametri, variabili, risorse e output a un modello. Queste funzionalità costituiscono i blocchi predefiniti di modelli molto più complessi.

> [!div class="nextstepaction"]
> [Aggiungere i parametri](template-tutorial-add-parameters.md)