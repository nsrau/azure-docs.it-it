---
title: Modalità di distribuzione di Azure Resource Manager| Microsoft Docs
description: Viene descritto come specificare se usare una modalità di distribuzione completa o incrementale con Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: b4d3a9c5e985949d813b638806f60bc86f5a163a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789261"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modelli di distribuzione Azure Resource Manager

Quando si distribuiscono le risorse, specificare se la distribuzione è un aggiornamento incrementale o completo.  La differenza principale tra le due modalità è il modo in cui Resource Manager gestisce le risorse esistenti nel gruppo di risorse che non sono presenti nel modello. La modalità predefinita è incrementale.

Per entrambe le modalità, Resource Manager prova a creare tutte le risorse specificate nel modello. Se la risorsa esiste già nel gruppo di risorse e le relative impostazioni sono identiche, non viene eseguita alcuna operazione per tale risorsa. Se si modificano i valori della proprietà per una risorsa, questa viene aggiornata con i nuovi valori. Se si prova ad aggiornare il percorso o il tipo di una risorsa esistente, la distribuzione ha esito negativo e restituisce un errore. È invece necessario distribuire una nuova risorsa con il percorso o il tipo necessari.

## <a name="complete-mode"></a>Modalità completa

Nella modalità di completamento, Resource Manager **elimina** le risorse esistenti nel gruppo di risorse che non sono specificate nel modello. Le risorse specificate nel modello, ma non distribuite in quanto una [condizione](resource-group-authoring-templates.md#condition) restituisce false, non vengono eliminate.

Esistono alcune differenze nel modo in cui i tipi di risorsa gestiscono la modalità completa le operazioni di eliminazione. Le risorse padre vengono eliminate automaticamente quando non sono specificate in un modello distribuito in modalità completa. Alcune risorse figlio non vengono eliminate automaticamente quando non sono specificate nel modello. Tuttavia, queste risorse figlio vengono eliminate se viene eliminata la risorsa padre. 

Ad esempio, se il gruppo di risorse contiene una zona DNS (tipo di risorsa Microsoft.Network/dnsZones) e un record CNAME (tipo di risorsa Microsoft.Network/dnsZones/CNAME), la zona DNS è la risorsa padre per il record CNAME. Se si distribuisce in la modalità completa e non si include la zona DNS nel modello, la zona DNS e il record CNAME vengono entrambi eliminati. Se si include la zona DNS nel modello ma non include il record CNAME, non viene eliminato il record CNAME. 

Per informazioni dettagliate sul modo in cui i tipi di risorsa gestiscono l'eliminazione, vedere [Eliminazione delle risorse di Azure per le distribuzioni in modalità completa](complete-mode-deletion.md).

Se il gruppo di risorse [bloccata](resource-group-lock-resources.md), la modalità completa non elimina le risorse.

> [!NOTE]
> Solo i modelli a livello di radice supportano la modalità di distribuzione completa. Per modelli [collegati o annidati](resource-group-linked-templates.md), è necessario usare la modalità di distribuzione incrementale. 
>
> [Le distribuzioni a livello di sottoscrizione](deploy-to-subscription.md) non supportano la modalità completa.
>
> Attualmente, il portale non supporta la modalità completa.
>

## <a name="incremental-mode"></a>Modalità incrementale

Nella modalità incrementale, Resource Manager **lascia invariate** le risorse esistenti nel gruppo di risorse che non sono specificate nel modello.

Tuttavia, quando si ridistribuisce una risorsa esistente nella modalità incrementale, il risultato è un altro. Specificare tutte le proprietà per la risorsa, non solo quelli che si sta aggiornando. Un malinteso comune consiste nel considerare le proprietà che non sono specificate sono rimane invariato. Se non si specificano determinate proprietà, Resource Manager interpreta l'aggiornamento come sovrascrittura di questi valori.

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
az group deployment create \
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
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
* Per informazioni sulla distribuzione delle risorse, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md).
* Per visualizzare le operazioni di un provider di risorse, vedere [Azure REST API](/rest/api/) (API REST di Azure).
