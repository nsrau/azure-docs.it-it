---
title: Distribuzione modelli simulazione (anteprima)
description: Determinare quali modifiche si verificheranno nelle risorse prima di distribuire un modello di Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: jgao
ms.openlocfilehash: 117215e7c41ad7f354c9e76f764e9af1f50b74c1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149235"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Gestione risorse l'operazione di simulazione della distribuzione del modello (anteprima)

Prima di distribuire un modello, è possibile visualizzare l'anteprima delle modifiche che si verificheranno. Azure Resource Manager fornisce l'operazione di simulazione per visualizzare il modo in cui le risorse vengono modificate se si distribuisce il modello. L'operazione di simulazione non consente di apportare modifiche alle risorse esistenti. Vengono invece stimate le modifiche se il modello specificato viene distribuito.

> [!NOTE]
> L'operazione di simulazione è attualmente in anteprima. Per usarlo, è necessario [iscriversi all'anteprima](https://aka.ms/armtemplatepreviews). Come versione di anteprima, i risultati possono a volte indicare che una risorsa cambierà quando in realtà non si verifica alcuna modifica. Ci stiamo impegnando per ridurre questi problemi, ma è necessario aiutarti. Segnalare questi problemi in [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).

È possibile usare l'operazione di simulazione con il comando `New-AzDeploymentWhatIf` PowerShell o le [distribuzioni-What If](/rest/api/resources/deployments/whatif) operazione Rest.

In PowerShell l'output ha un aspetto simile al seguente:

![Gestione risorse l'operazione di simulazione della distribuzione del modello fullresourcepayload e i tipi di modifica](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Tipi di modifiche

L'operazione di simulazione elenca sei tipi diversi di modifiche:

- **Create**: la risorsa attualmente non esiste ma è definita nel modello. La risorsa verrà creata.

- **Elimina**: questo tipo di modifica si applica solo quando si usa la [modalità completa](deployment-modes.md) per la distribuzione. La risorsa esiste, ma non è definita nel modello. Con la modalità completa, la risorsa verrà eliminata. Solo le risorse che [supportano l'eliminazione in modalità completa](complete-mode-deletion.md) sono incluse in questo tipo di modifica.

- **Ignore**: la risorsa esiste, ma non è definita nel modello. La risorsa non verrà distribuita o modificata.

- **NoChange**: la risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita, ma le proprietà della risorsa non verranno modificate. Questo tipo di modifica viene restituito quando [ResultFormat](#result-format) è impostato su `FullResourcePayloads`, che corrisponde al valore predefinito.

- **Modifica**: la risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita e le proprietà della risorsa verranno modificate. Questo tipo di modifica viene restituito quando [ResultFormat](#result-format) è impostato su `FullResourcePayloads`, che corrisponde al valore predefinito.

- **Deploy**: la risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita. Le proprietà della risorsa possono essere modificate o meno. Tramite l'operazione viene restituito questo tipo di modifica quando non sono disponibili informazioni sufficienti per determinare se le proprietà cambiano. Questa condizione viene visualizzata solo quando [ResultFormat](#result-format) è impostato su `ResourceIdOnly`.

## <a name="deployment-scope"></a>Ambito di distribuzione

È possibile usare l'operazione di simulazione per le distribuzioni a livello di sottoscrizione o di gruppo di risorse. L'ambito di distribuzione viene impostato con il parametro `-ScopeType`. I valori accettati sono `Subscription` e `ResourceGroup`. Questo articolo illustra le distribuzioni del gruppo di risorse.

Per informazioni sulle distribuzioni a livello di sottoscrizione, vedere [creare gruppi di risorse e risorse a livello di sottoscrizione](deploy-to-subscription.md#).

## <a name="result-format"></a>Formato risultato

È possibile controllare il livello di dettaglio restituito sulle modifiche previste. Impostare il parametro `ResultFormat` su `FullResourcePayloads` per ottenere un elenco di risorse che cambieranno e dettagli sulle proprietà che cambieranno. Impostare il parametro `ResultFormat` su `ResourceIdOnly` per ottenere un elenco di risorse che cambieranno. Il valore predefinito è `FullResourcePayloads`.  

Gli screenshot seguenti mostrano i due diversi formati di output:

- Payload di risorse completi

    ![Gestione risorse l'operazione di simulazione della distribuzione del modello fullresourcepayloads output](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Solo ID risorsa

    ![Gestione risorse l'operazione di simulazione della distribuzione del modello resourceidonly output](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Eseguire un'operazione di simulazione

### <a name="set-up-environment"></a>Configurare l'ambiente

Per verificarne il funzionamento, è possibile eseguire alcuni test. Per prima cosa, distribuire un modello da [modelli di avvio rapido di Azure che crea un account di archiviazione](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json). Il tipo di account di archiviazione predefinito è `Standard_LRS`. Questo account di archiviazione verrà usato per verificare il modo in cui vengono segnalate le modifiche da simulazione.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Modifica test

Al termine della distribuzione, si è pronti per testare l'operazione di simulazione. Eseguire il comando simulazione, ma modificare il tipo di account di archiviazione in `Standard_GRS`.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

L'output di simulazione è simile al seguente:

![Gestione risorse l'output dell'operazione di simulazione della distribuzione del modello](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Si noti che nella parte superiore dell'output vengono definiti i colori per indicare il tipo di modifiche.

Nella parte inferiore dell'output viene visualizzato il nome dello SKU (tipo di account di archiviazione) che verrà modificato da **Standard_LRS** a **Standard_GRS**.

### <a name="test-deletion"></a>Eliminazione del test

L'operazione di simulazione supporta l'uso della [modalità di distribuzione](deployment-modes.md). Quando viene impostata la modalità completa, le risorse non presenti nel modello vengono eliminate. Nell'esempio seguente viene distribuito un [modello privo di risorse definite](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in modalità completa.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Poiché nel modello non sono definite risorse e la modalità di distribuzione è impostata per il completamento, l'account di archiviazione verrà eliminato.

![Modalità di distribuzione dell'output dell'operazione di distribuzione del modello di Gestione risorse completamento](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

È importante ricordare cosa-se non apporta modifiche effettive. L'account di archiviazione esiste ancora nel gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

- Se si notano risultati non corretti dalla versione di anteprima di simulazione, segnalare i problemi in [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues).
- Per distribuire i modelli con Azure PowerShell, vedere [distribuire le risorse con i modelli e Azure PowerShell di gestione risorse](resource-group-template-deploy.md).
- Per distribuire i modelli con REST, vedere [distribuire le risorse con gestione risorse modelli e gestione risorse API REST](resource-group-template-deploy-rest.md).
- Per eseguire il rollback a una distribuzione corretta quando viene visualizzato un errore, vedere eseguire [il rollback in caso di errore di distribuzione riuscita](rollback-on-error.md).
