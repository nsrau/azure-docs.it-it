---
title: Procedura di distribuzione dei modelli (anteprima)Template deployment what-if (Preview)
description: Determinare quali modifiche verranno apportate alle risorse prima di distribuire un modello di Azure Resource Manager.Determine what changes will happen to your resources before deploying an Azure Resource Manager template.
author: mumian
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: 9e0d0d572e08961b585a93e66e400b8c2e54bf7f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886841"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operazione di creazione di elementi di creazione di modelli ARM (anteprima)ARM template deployment what-if operation (Preview)

Prima di distribuire un modello di Azure Resource Manager (ARM), è consigliabile visualizzare in anteprima le modifiche che si verificheranno. Azure Resource Manager offre l'operazione di tipo per consentire di vedere come cambieranno le risorse se si distribuisce il modello. L'operazione di creazione di un'ora non apporta alcuna modifica alle risorse esistenti. Al contrario, prevede le modifiche se viene distribuito il modello specificato.

> [!NOTE]
> L'operazione di creazione di un'ora è attualmente in anteprima. Come versione di anteprima, i risultati possono talvolta mostrare che una risorsa cambierà quando in realtà non verrà apportata alcuna modifica. Stiamo lavorando per ridurre questi problemi, ma abbiamo bisogno del tuo aiuto. Si prega di [https://aka.ms/whatifissues](https://aka.ms/whatifissues)segnalare questi problemi a .

È possibile usare l'operazione what-if con i comandi di PowerShell o le operazioni dell'API REST.

## <a name="install-powershell-module"></a>Installare il modulo di PowerShellInstall PowerShell module

Per usare what-if in PowerShell, installare una versione di anteprima del modulo Az.Resources dalla raccolta di PowerShell.To use what-if in PowerShell, install a preview version of the Az.Resources module from the PowerShell gallery.

### <a name="uninstall-alpha-version"></a>Disinstallare la versione alpha

Se in precedenza è stata installata una versione alfa del modulo what-if, disinstallare tale modulo. La versione alpha era disponibile solo per gli utenti che si sono iscritti per un'anteprima anticipata. Se non hai installato l'anteprima, puoi saltare questa sezione.

1. Eseguire PowerShell come amministratore
1. Controllare le versioni installate del modulo Az.Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Se si dispone di una versione installata con un numero di versione nel formato **2.x.x-alpha**, disinstallare tale versione.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Annullare la registrazione del repository di tipo what-if utilizzato per installare l'anteprima.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

### <a name="install-preview-version"></a>Installare la versione di anteprima

Per installare il modulo di anteprima, utilizzare:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

Sei pronto per usare what-if.

## <a name="see-results"></a>See results (Visualizza risultati)

In PowerShell l'output include risultati codificati a colori che consentono di visualizzare i diversi tipi di modifiche.

![Distribuzione di tipi di creazione di tipi di what-if fullresourcepayload e tipi di modifica](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Comandi di te-if

È possibile usare Azure PowerShell o l'API REST di Azure per l'operazione what-if.

### <a name="azure-powershell"></a>Azure PowerShell

Per visualizzare un'anteprima delle modifiche prima di `-Whatif` distribuire un modello, aggiungere il parametro switch al comando di distribuzione.

* `New-AzResourceGroupDeployment -Whatif`per le distribuzioni di gruppi di risorse
* `New-AzSubscriptionDeployment -Whatif`e `New-AzDeployment -Whatif` per le distribuzioni a livello di sottoscrizione

In alternativa, è `-Confirm` possibile utilizzare il parametro switch per visualizzare in anteprima le modifiche e ricevere la richiesta di continuare con la distribuzione.

* `New-AzResourceGroupDeployment -Confirm`per le distribuzioni di gruppi di risorse
* `New-AzSubscriptionDeployment -Confirm`e `New-AzDeployment -Confirm` per le distribuzioni a livello di sottoscrizione

I comandi precedenti restituiscono un riepilogo di testo che è possibile esaminare manualmente. Per ottenere un oggetto che è possibile controllare a livello di codice per le modifiche, utilizzare:To get an object that you can programmatically inspect for changes, use:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`per le distribuzioni di gruppi di risorse
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`o `$results = Get-AzDeploymentWhatIfResult` per le distribuzioni a livello di sottoscrizione

### <a name="azure-rest-api"></a>API REST di Azure

Per l'API REST, usare:For REST API, use:

* [Distribuzioni - What If](/rest/api/resources/deployments/whatif) per le distribuzioni di gruppi di risorse
* [Distribuzioni - Cosa succede se l'ambito della sottoscrizione](/rest/api/resources/deployments/whatifatsubscriptionscope) per le distribuzioni a livello di sottoscrizioneDeployments - What If Subscription Scope for subscription level deployments

## <a name="change-types"></a>Modificare i tipi

L'operazione di creazione di un'operazione elenca sei diversi tipi di modifiche:

- **Crea:** la risorsa attualmente non esiste ma è definita nel modello. La risorsa verrà creata.

- **Elimina:** questo tipo di modifica si applica solo quando si utilizza la [modalità completa](deployment-modes.md) per la distribuzione. La risorsa esiste, ma non è definita nel modello. Con la modalità completa, la risorsa verrà eliminata. Solo le risorse che [supportano l'eliminazione in modalità completa](complete-mode-deletion.md) sono incluse in questo tipo di modifica.

- **Ignora:** la risorsa esiste, ma non è definita nel modello. La risorsa non verrà distribuita o modificata.

- **NoChange**: La risorsa esiste ed è definita nel modello. La risorsa verrà ridistribuita, ma le proprietà della risorsa non verranno modificate. Questo tipo di modifica viene restituito `FullResourcePayloads`quando [ResultFormat](#result-format) è impostato su , che è il valore predefinito.

- **Modifica**: La risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita e le proprietà della risorsa verranno modificate. Questo tipo di modifica viene restituito `FullResourcePayloads`quando [ResultFormat](#result-format) è impostato su , che è il valore predefinito.

- **Distribuisci**: La risorsa esiste e viene definita nel modello. La risorsa verrà ridistribuita. Le proprietà della risorsa possono modificare o meno. L'operazione restituisce questo tipo di modifica quando non dispone di informazioni sufficienti per determinare se le proprietà verranno modificate. Questa condizione viene visualizzata solo `ResourceIdOnly`quando [ResultFormat](#result-format) è impostato su .

## <a name="result-format"></a>Formato del risultato

È possibile controllare il livello di dettaglio restituito sulle modifiche previste. Nei comandi di`New-Az*Deployment`distribuzione ( ), utilizzare il parametro **-WhatIfResultFormat** . Nei comandi dell'oggetto`Get-Az*DeploymentWhatIf`a livello di codice ( ), utilizzare il parametro **ResultFormat.**

Impostare il parametro format su **FullResourcePayloads** per ottenere un elenco di risorse che verranno modificate e dettagli sulle proprietà che verranno modificate. Impostare il parametro format su **ResourceIdOnly** per ottenere un elenco di risorse che verranno modificate. Il valore predefinito è **FullResourcePayloads**.  

I seguenti risultati mostrano i due diversi formati di output:

- Payload di risorse complete

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Solo ID risorsa

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Eseguire un'operazione di tipo i-ifRun what-if operation

### <a name="set-up-environment"></a>Set up environment (Configurare l'ambiente)

Per vedere come what-if funziona, cerchiamo di eseguire alcuni test. Innanzitutto, distribuire un [modello che crea una rete virtuale.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Questa rete virtuale verrà usata per testare il modo in cui le modifiche vengono segnalate da what-if.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modifica del test

Al termine della distribuzione, è possibile testare l'operazione di creazione. Questa volta distribuire un [modello che modifica la rete virtuale.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) Manca uno dei tag originali, una subnet è stata rimossa e il prefisso dell'indirizzo è stato modificato.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

L'output di what-if appare simile a:

![Output dell'operazione di creazione di what-if del modello di Resource ManagerResource Manager template deployment what-if operation output](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Si noti che nella parte superiore dell'output i colori sono definiti per indicare il tipo di modifiche.

Nella parte inferiore dell'output, mostra il tag Owner è stato eliminato. Il prefisso dell'indirizzo è stato modificato da 10.0.0.0/16 a 10.0.0.0/15. La subnet denominata subnet001 è stata eliminata. Tenere presente che queste modifiche non sono state effettivamente distribuite. Viene visualizzata un'anteprima delle modifiche che si verificheranno se si distribuisce il modello.

Alcune delle proprietà elencate come eliminate non verranno effettivamente modificate. Le proprietà possono essere erroneamente segnalate come eliminate quando non sono nel modello, ma vengono impostate automaticamente durante la distribuzione come valori predefiniti. Questo risultato è considerato "rumore" nella risposta what-if. La risorsa distribuita finale avrà i valori impostati per le proprietà. Man mano che l'operazione what-if matura, queste proprietà verranno filtrate dal risultato.

## <a name="programmatically-evaluate-what-if-results"></a>Valutare a livello di codice i risultati i-if

A questo punto, è possibile valutare a livello di codice i risultati di what-if impostando il comando su una variabile.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

È possibile visualizzare un riepilogo di ogni modifica.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Confermare l'eliminazione

L'operazione di creazione di un'ora supporta l'utilizzo della [modalità di distribuzione.](deployment-modes.md) Se impostato sulla modalità completa, le risorse non presenti nel modello vengono eliminate. Nell'esempio seguente viene distribuito un [modello senza risorse definite](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) in modalità completa.

Per visualizzare in anteprima le modifiche `-Confirm` prima di distribuire un modello, usare il parametro switch con il comando deployment. Se le modifiche sono come previsto, confermare che si desidera completare la distribuzione.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Poiché nel modello non è definita alcuna risorsa e la modalità di distribuzione è impostata su complete, la rete virtuale verrà eliminata.

![Completamento della modalità di distribuzione dell'output dell'operazione di Resource ManagerResource Manager template deployment what-if operation output deployment mode complete](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

L'output di testo è:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Vengono visualizzate le modifiche previste e si può confermare che si desidera eseguire la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- Se noti risultati errati dalla versione di anteprima [https://aka.ms/whatifissues](https://aka.ms/whatifissues)di what-if, segnala i problemi all'indirizzo .
- Per distribuire modelli con Azure PowerShell, vedere [Distribuire risorse con modelli ARM e Azure PowerShell.](deploy-powershell.md)
- Per distribuire modelli con REST, vedere [Distribuire risorse con i modelli ARM e L'API REST](deploy-rest.md)di Resource Manager.
