---
title: 'Esercitazione: Aggiungere parametri al modello'
description: Aggiungere parametri al modello di Azure Resource Manager per renderlo riutilizzabile.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 28c171dfa067ec9b3eff2e0d7e5d5dd0a0c274c0
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406081"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Esercitazione: Aggiungere parametri al modello di Resource Manager

Nell'[esercitazione precedente](template-tutorial-add-resource.md) è stato illustrato come aggiungere un account di archiviazione al modello e distribuirlo. Questa esercitazione illustra come migliorare il modello aggiungendo parametri. Per completare l'esercitazione, sono necessari circa **14 minuti**.

## <a name="prerequisites"></a>Prerequisiti

È consigliabile, ma non obbligatorio, completare l'[esercitazione sulle risorse](template-tutorial-add-resource.md).

È necessario avere Visual Studio Code con l'estensione Strumenti di Resource Manager e Azure PowerShell oppure l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere gli [strumenti per i modelli](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Rivedere il modello

Al termine dell'esercitazione precedente, il modello includeva il codice JSON seguente:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

Si sarà probabilmente notato che si è verificato un problema con questo modello. Il nome dell'account di archiviazione è hardcoded. Questo modello può quindi essere usato solo per distribuire ogni volta lo stesso account di archiviazione. Per distribuire un account di archiviazione con un nome diverso, è necessario creare un nuovo modello e questa non è certo una soluzione pratica per automatizzare le distribuzioni.

## <a name="make-template-reusable"></a>Rendere riutilizzabile il modello

Per rendere riutilizzabile il modello, si aggiungerà un parametro che è possibile usare per passare un nome dell'account di archiviazione. Il codice JSON evidenziato nell'esempio seguente illustra le modifiche apportate nel modello. Il parametro **storageName** viene identificato come stringa. La lunghezza massima è impostata su 24 caratteri per impedire nomi troppo lunghi.

Copiare l'intero file e sostituire il modello con il contenuto del file.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-template"></a>Distribuire il modello

A questo punto è possibile distribuire il modello. L'esempio seguente illustra come distribuire il modello con l'interfaccia della riga di comando di Azure o con PowerShell. Si noti che il nome dell'account di archiviazione viene specificato come uno dei valori nel comando di distribuzione. Per il nome dell'account di archiviazione specificare lo stesso nome usato nell'esercitazione precedente.

Se non è stato ancora creato il gruppo di risorse, vedere [Creare il gruppo di risorse](template-tutorial-create-first-template.md#create-resource-group). Nell'esempio si presuppone che la variabile **templateFile** sia stata impostata sul percorso del file modello, come illustrato nella [prima esercitazione](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Informazioni sugli aggiornamenti delle risorse

Nella sezione precedente è stato distribuito un account di archiviazione con lo stesso nome creato in precedenza. È possibile che ci si chieda quale sia l'impatto della ridistribuzione sulla risorsa.

Se la risorsa esiste già e non viene rilevata alcuna modifica nelle proprietà, non viene eseguita alcuna azione. Se la risorsa esiste già ed è stata modificata una proprietà, la risorsa viene aggiornata. Se la risorsa non esiste, viene creata.

Questa modalità di gestione degli aggiornamenti indica che il modello può includere tutte le risorse necessarie per una soluzione di Azure. È possibile ridistribuire il modello in modo sicuro ed essere certi che le risorse verranno modificate o create solo quando necessario. Se, ad esempio, sono stati aggiunti file all'account di archiviazione, è possibile ridistribuire l'account di archiviazione senza perdere tali file.

## <a name="customize-by-environment"></a>Personalizzare in base all'ambiente

I parametri consentono di personalizzare la distribuzione fornendo valori specifici per un determinato ambiente. È, ad esempio, possibile passare valori diversi a seconda che si stia eseguendo la distribuzione in un ambiente di sviluppo, test o produzione.

Con il modello precedente veniva sempre distribuito un account di archiviazione di tipo Archiviazione con ridondanza locale Standard (Standard_LRS). Si potrebbe però voler distribuire SKU diversi a seconda dell'ambiente. Nell'esempio seguente sono illustrate le modifiche da apportare per aggiungere un parametro per lo SKU. Copiare l'intero file e incollarlo nel modello sovrascrivendolo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

Al parametro **storageSKU** è assegnato un valore predefinito. Questo valore viene usato quando non si specifica un valore durante la distribuzione. Include anche un elenco di valori consentiti. Questi valori corrispondono a quelli necessari per creare un account di archiviazione. Non si vuole che gli utenti del modello passino SKU che non funzionano.

## <a name="redeploy-template"></a>Ridistribuire il modello

È ora possibile distribuire di nuovo l'app. Dal momento che lo SKU predefinito è impostato su **Standard_LRS**, non è necessario specificare un valore per tale parametro.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

A questo punto il modello verrà ridistribuito per verificarne la flessibilità. Questa volta impostare il parametro SKU su **Standard_GRS**. È possibile passare un nuovo nome per creare un account di archiviazione diverso oppure usare lo stesso nome per aggiornare l'account di archiviazione esistente. Entrambe le opzioni sono ugualmente valide.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Eseguire infine un ulteriore test e verificare cosa succede quando si passa uno SKU che non corrisponde a uno dei valori consentiti. In questo caso, si testerà lo scenario in cui un utente del modello ritiene che **basic** sia uno degli SKU.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Il comando non viene eseguito e viene visualizzato subito un messaggio di errore che indica i valori consentiti. Resource Manager identifica l'errore prima dell'avvio della distribuzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende passare all'esercitazione successiva, non è necessario eliminare il gruppo di risorse.

Se invece ci si ferma, è opportuno eliminare il gruppo di risorse per rimuovere le risorse distribuite.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

## <a name="next-steps"></a>Passaggi successivi

Sono stati aggiunti parametri per migliorare il modello creato nella [prima esercitazione](template-tutorial-create-first-template.md). Nell'esercitazione successiva verranno illustrate le funzioni del modello.

> [!div class="nextstepaction"]
> [Aggiungere le funzioni del modello](template-tutorial-add-functions.md)
