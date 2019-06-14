---
title: Come creare un modello di Azure Resource Manager
description: Descrive il processo per creare un modello di Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: b198f860c10bd207a191057e21a8a159c5ebde26
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551003"
---
# <a name="create-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Questo articolo descrive il processo e le decisioni da prendere per creare un modello di Azure Resource Manager. Fornisce una panoramica di esempi e funzionalità utili durante la creazione del modello. L'articolo presuppone che si distribuiscano risorse a un gruppo di risorse. Se è necessario distribuire le risorse alla sottoscrizione di Azure, ad esempio per la creazione dei criteri di Azure o le assegnazioni di controlli degli accessi in base al ruolo, vedere [Creare gruppi di risorse e risorse per una sottoscrizione di Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Selezionare l'editor JSON

Il modello di Azure Resource Manager è un file JSON. Per eseguire operazioni sul file JSON, è necessario un ottimo strumento di creazione. Ci sono molte opzioni a disposizione, ma se non si ha un editor preferito è consigliabile installare [Visual Studio Code (VS Code)](https://code.visualstudio.com/). 

Dopo aver installato Visual Studio Code, aggiungere l'[estensione Strumenti di Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Questa estensione aggiunge molte funzionalità che semplificano la creazione del modello.

![Modello in Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

Lo screenshot mostra un modello di Resource Manager aperto in Visual Studio Code. 

Per un'esercitazione sull'installazione dell'estensione Strumenti di Resource Manager e su come usare VS Code, vedere [Avvio rapido: Creare modelli di Azure Resource Manager con Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Comprendere la struttura del modello

Esaminiamo ora le parti del modello per comprendere il suo funzionamento. Il modello potrebbe non avere tutte le sezioni. Le sezioni che su cui si desidera concentrarsi sono:

* La sezione dei [parameters](resource-group-authoring-templates.md#parameters), che mostra i valori che possono essere specificati durante la distribuzione per personalizzare l'infrastruttura distribuita. 

* La sezione delle [variables](resource-group-authoring-templates.md#variables), che mostra i valori usati nel modello.

* La sezione delle [functions](resource-group-authoring-templates.md#functions), che mostra le espressioni personalizzate usate nel modello.

* La sezione delle [resources](resource-group-authoring-templates.md#resources), che mostra le risorse di Azure distribuite alla sottoscrizione.

* La sezione degli [outputs](resource-group-authoring-templates.md#outputs), che mostra i valori restituiti al termine della distribuzione.

## <a name="look-for-similar-templates"></a>Cercare modelli simili

È spesso possibile trovare un modello esistente che distribuisce una soluzione simile a quella che serve. I [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) offrono centinaia di modelli creati dai collaboratori della comunità.

![Repository di modelli di avvio rapido](./media/how-to-create-template/template-quickstart-repo.png)

Cercare nel repository un modello simile a quello che serve. Anche se il modello non corrisponde esattamente a quello che serve, va bene comunque perché può essere personalizzato.

Dopo aver trovato un modello, selezionare **Sfoglia su Github**, quindi copiare il file **azuredeploy.json** dal repository. In VS Code creare un nuovo file denominato **azuredeploy.json** e aggiungere i contenuti del file del modello copiato dal repository di avvio rapido.

## <a name="add-resources"></a>Aggiungere risorse

Probabilmente si vorrà personalizzare il modello per verificare che faccia esattamente ciò che si desidera. Prima esaminare le risorse che vengono distribuite. Potrebbe essere necessario aggiungere, rimuovere o modificare le risorse nel modello. Per trovare le descrizioni e la sintassi delle risorse, vedere [Azure Resource Manager template reference](/azure/templates/) (Documento di riferimento sui modelli di Azure Resource Manager).

![Informazioni di riferimento sul modello](./media/how-to-create-template/template-reference.png)

Dopo aver esaminato queste proprietà, apportare le modifiche necessarie. Per consigli su come definire le risorse, vedere [resources - recommended practices](template-best-practices.md#resources) (Risorse - Procedure consigliate).

## <a name="add-or-remove-parameters"></a>Aggiungere o rimuovere parametri

Potrebbe anche essere necessario regolare i parametri per il modello. È possibile aggiungere o rimuovere parametri in base alla quantità di personalizzazione che si desidera abilitare durante la distribuzione. Per consigli su come usare i parametri, vedere [parameters - recommended practices](template-best-practices.md#parameters) (Parametri - Procedure consigliate).

## <a name="add-tags"></a>Aggiungi tag

È possibile aggiungere tag alle risorse per organizzare in modo logico in base alle categorie e dividere i costi di fatturazione. Aggiungere tag è facile: si applicano nel file JSON per la risorsa. Ad esempio, l'account di archiviazione seguente presenta due tag:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

È anche possibile applicare tag in modo dinamico dai parametri. Per altre informazioni, vedere [Tag nel modello](resource-group-using-tags.md#templates).

## <a name="review-template-functions"></a>Esaminare le funzioni del modello

Nel modello è possibile notare espressioni racchiuse tra parentesi quadre, ad esempio `"[some-expression]"`. Queste espressioni usano funzioni del modello per costruire in modo dinamico i valori durante la distribuzione.

Ad esempio, spesso viene visualizzata un'espressione come:

```json
"name": "[parameters('siteName')]"
```

L'espressione ottiene il valore di un parametro. Il valore viene assegnato alla proprietà del nome.

In alternativa, è possibile visualizzare un'espressione più complessa che usa diverse funzioni, ad esempio:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Tale espressione ottiene un oggetto con le proprietà di un account di archiviazione.

Per capire cosa fanno le funzioni, esaminare la documentazione di [riferimento sulle funzioni del modello](resource-group-template-functions.md).

## <a name="create-more-than-one-instance"></a>Creare più di un'istanza

Talvolta potrebbe essere necessario creare più istanze di una risorsa. Ad esempio, potrebbero servire vari account di archiviazione. Piuttosto che ripetere la risorsa tramite il modello, è possibile usare la sintassi `copy` per specificare più di un'istanza.

L'esempio seguente crea tre account di archiviazione:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

È anche possibile specificare il numero di istanze in modo dinamico da un parametro. Per altre informazioni, vedere [Distribuire più istanze di una risorsa o di una proprietà nei modelli di Azure Resource Manager](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Distribuire una risorsa in modo condizionale

In alcuni casi è necessario specificare durante la distribuzione se una risorsa nel modello viene distribuita. Ad esempio, potrebbe servire la flessibilità di distribuire una nuova risorsa oppure usarne una esistente. L'elemento `condition` offre la possibilità di attivare o disattivare la distribuzione per una risorsa. Quando l'espressione nell'elemento di condizione è true, la risorsa viene distribuita. Quando è false, la risorsa viene saltata durante la distribuzione.

L'esempio seguente distribuisce un account di archiviazione in modo condizionale:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Per altre informazioni, vedere l'[elemento di condizione](resource-group-authoring-templates.md#condition).

## <a name="review-dependencies"></a>Esaminare le dipendenze

Alcune risorse nel modello devono essere distribuite prima di altre. Ad esempio, SQL Server deve esistere prima della creazione del database SQL. Resource Manager determina in modo implicito l'ordine di distribuzione per le risorse quando viene usata la [funzione di riferimento](resource-group-template-functions-resource.md#reference). Tuttavia in alcuni casi è necessario definire in modo esplicito le dipendenze usando l'elemento `dependsOn`. Esaminare il modello per verificare se è necessario aggiungere eventuali dipendenze. Prestare attenzione a non aggiungere dipendenze non necessarie, perché possono rallentare la distribuzione o creare riferimenti circolari.

L'immagine seguente mostra l'ordine di dipendenza per varie risorse del servizio app:

![Dipendenze delle app Web](./media/how-to-create-template/web-dependencies.png)

L'esempio seguente mostra parte di un modello che definisce le dipendenze.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Per altre informazioni, leggere [Definire l'ordine per la distribuzione delle risorse nei modelli di Azure Resource Manager](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Leggere le procedure consigliate

Prima di distribuire il modello, leggere l'articolo [Azure Resource Manager template best practices](template-best-practices.md) (Procedure consigliate per i modelli di Azure Resource Manager) per verificare se ci sono approcci consigliati da implementare nel modello.

Se è necessario usare il modello in ambienti cloud diversi di Azure, vedere [Sviluppare modelli di Azure Resource Manager per la coerenza del cloud](templates-cloud-consistency.md).

## <a name="next-steps"></a>Passaggi successivi

* Per distribuire un modello, vedere [Distribuire con l'interfaccia della riga di comando di Azure](resource-group-template-deploy-cli.md) oppure [Distribuire con PowerShell](resource-group-template-deploy.md).
* Per istruzioni rapide dettagliate sulla creazione di un modello, vedere [Creare modelli di Azure Resource Manager con Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](resource-group-template-functions.md).
