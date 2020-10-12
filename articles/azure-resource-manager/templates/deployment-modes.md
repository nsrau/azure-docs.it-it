---
title: Modalità di distribuzione
description: Viene descritto come specificare se usare una modalità di distribuzione completa o incrementale con Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: e584acd4af1dc6adb5f5d383acd5d16da0815f32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371584"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modelli di distribuzione Azure Resource Manager

Quando si distribuiscono le risorse, specificare se la distribuzione è un aggiornamento incrementale o completo. La differenza tra queste due modalità è il modo in cui Gestione risorse gestisce le risorse esistenti nel gruppo di risorse che non sono incluse nel modello.

Per entrambe le modalità, Resource Manager prova a creare tutte le risorse specificate nel modello. Se la risorsa esiste già nel gruppo di risorse e le relative impostazioni sono identiche, non viene eseguita alcuna operazione per tale risorsa. Se si modificano i valori della proprietà per una risorsa, questa viene aggiornata con i nuovi valori. Se si prova ad aggiornare il percorso o il tipo di una risorsa esistente, la distribuzione ha esito negativo e restituisce un errore. È invece necessario distribuire una nuova risorsa con il percorso o il tipo necessari.

La modalità predefinita è incrementale.

## <a name="complete-mode"></a>Modalità completa

Nella modalità di completamento, Resource Manager **elimina** le risorse esistenti nel gruppo di risorse che non sono specificate nel modello.

> [!NOTE]
> Usare sempre l' [operazione](template-deploy-what-if.md) di simulazione prima di distribuire un modello in modalità completa. Cosa-se Mostra le risorse che verranno create, eliminate o modificate. Usare il tipo di simulazione per evitare l'eliminazione involontaria delle risorse.

Se il modello include una risorsa che non viene distribuita perché la [condizione](conditional-resource-deployment.md) restituisce false, il risultato dipende dalla versione dell'API REST usata per distribuire il modello. Se si usa una versione precedente alla 2019-05-10, la risorsa **non viene eliminata**. Con 2019-05-10 o versioni successive, la risorsa **viene eliminata**. Le versioni più recenti di Azure PowerShell e dell'interfaccia della riga di comando di Azure Elimina la risorsa.

Prestare attenzione usando la modalità completa con i [cicli di copia](copy-resources.md). Tutte le risorse non specificate nel modello dopo la risoluzione del ciclo di copia verranno eliminate.

Se si esegue la distribuzione in [più di un gruppo di risorse in un modello](cross-scope-deployment.md), le risorse nel gruppo di risorse specificato nell'operazione di distribuzione sono idonee per essere eliminate. Le risorse nei gruppi di risorse secondarie non vengono eliminate.

Esistono alcune differenze nel modo in cui i tipi di risorse gestiscono le eliminazioni in modalità completa. Le risorse padre vengono eliminate automaticamente quando non sono specificate in un modello distribuito in modalità completa. Alcune risorse figlio non vengono eliminate automaticamente quando non sono specificate nel modello. Tuttavia, queste risorse figlio vengono eliminate se la risorsa padre viene eliminata.

Ad esempio, se il gruppo di risorse contiene una zona DNS (tipo di risorsa Microsoft.Network/dnsZones) e un record CNAME (tipo di risorsa Microsoft.Network/dnsZones/CNAME), la zona DNS è la risorsa padre per il record CNAME. Se si distribuisce in la modalità completa e non si include la zona DNS nel modello, la zona DNS e il record CNAME vengono entrambi eliminati. Se si include la zona DNS nel modello, ma non si include il record CNAME, il CNAME non viene eliminato.

Per informazioni dettagliate sul modo in cui i tipi di risorsa gestiscono l'eliminazione, vedere [Eliminazione delle risorse di Azure per le distribuzioni in modalità completa](complete-mode-deletion.md).

Se il gruppo di risorse è [bloccato](../management/lock-resources.md), la modalità di completamento non comporta l'eliminazione delle risorse.

> [!NOTE]
> Solo i modelli a livello di radice supportano la modalità di distribuzione completa. Per modelli [collegati o annidati](linked-templates.md), è necessario usare la modalità di distribuzione incrementale.
>
> Le [distribuzioni a livello di sottoscrizione](deploy-to-subscription.md) non supportano la modalità completa.
>
> Attualmente, il portale non supporta la modalità completa.
>

## <a name="incremental-mode"></a>Modalità incrementale

Nella modalità incrementale, Resource Manager **lascia invariate** le risorse esistenti nel gruppo di risorse che non sono specificate nel modello. Le risorse nel modello **vengono aggiunte** al gruppo di risorse.

> [!NOTE]
> Quando si ridistribuisce una risorsa esistente in modalità incrementale, tutte le proprietà vengono riapplicate. Le **proprietà non vengono aggiunte in modo incrementale**. Un equivoco comune è pensare che le proprietà che non sono specificate nel modello rimangano invariate. Se non si specificano determinate proprietà, Gestione risorse interpreta la distribuzione come sovrascrivendo tali valori. Le proprietà che non sono incluse nel modello vengono reimpostate sui valori predefiniti. Specificare tutti i valori non predefiniti per la risorsa, non solo quelli che si sta aggiornando. La definizione di risorsa nel modello contiene sempre lo stato finale della risorsa. Non può rappresentare un aggiornamento parziale di una risorsa esistente.
>
> In rari casi, le proprietà specificate per una risorsa vengono effettivamente implementate come una risorsa figlio. Ad esempio, quando si specificano i valori di configurazione del sito per un'app Web, tali valori vengono implementati nel tipo di risorsa figlio `Microsoft.Web/sites/config` . Se si ridistribuisce l'app Web e si specifica un oggetto vuoto per i valori di configurazione del sito, la risorsa figlio non viene aggiornata. Tuttavia, se si specificano nuovi valori di configurazione del sito, il tipo di risorsa figlio viene aggiornato.

## <a name="example-result"></a>Risultati di esempio

Per illustrare la differenza tra le modalità incrementale e completa, si consideri lo scenario seguente.

**Il gruppo di risorse** contiene:

* Risorsa A
* Risorsa B
* Risorsa C

**Modello** contiene:

* Risorsa A
* Risorsa B
* Risorsa D

Quando viene implementato in modalità **incrementale**, il gruppo di risorse contiene:

* Risorsa A
* Risorsa B
* Risorsa C
* Risorsa D

Quando viene implementato in modalità **completa**, la risorsa C viene eliminata. Il gruppo di risorse contiene:

* Risorsa A
* Risorsa B
* Risorsa D

## <a name="set-deployment-mode"></a>Impostare la modalità di distribuzione

Per impostare la modalità di distribuzione durante la distribuzione con PowerShell, usare il parametro `Mode`.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Per impostare la modalità di distribuzione durante la distribuzione con interfaccia della riga di comando di Azure, usare il parametro `mode`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

L'esempio seguente illustra un modello collegato impostato sulla modalità di distribuzione incrementale:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla creazione di modelli di Gestione risorse, vedere Creazione di modelli di [Azure Resource Manager](template-syntax.md).
* Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](deploy-powershell.md).
* Per visualizzare le operazioni di un provider di risorse, vedere [Azure REST API](/rest/api/) (API REST di Azure).
