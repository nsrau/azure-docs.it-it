---
title: Importare ed esportare definizioni di progetto con PowerShell
description: Informazioni su come usare le definizioni di progetto come codice. Condividere, controllare il codice sorgente e gestirli usando i comandi Export e Import.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/03/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f7bc3610841bcc3c40435f077073ffa0d55acd93
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243178"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importare ed esportare definizioni di progetto con PowerShell

I progetti di Azure possono essere completamente gestiti tramite portale di Azure. Man mano che le organizzazioni avanzano nell'uso dei progetti, devono iniziare a pensare alle definizioni di progetto come codice gestito. Questo concetto è spesso definito infrastruttura come codice (IaC). Trattare le definizioni del progetto in quanto il codice offre vantaggi aggiuntivi oltre a quelle offerte da portale di Azure. Questi vantaggi includono:

- Condivisione delle definizioni del progetto
- Backup delle definizioni del progetto
- Riutilizzo di definizioni di progetto in diversi tenant o sottoscrizioni
- Inserimento delle definizioni del progetto nel controllo del codice sorgente
  - Test automatizzato delle definizioni di progetto negli ambienti di test
  - Supporto delle pipeline di integrazione continua e distribuzione continua (CI/CD)

Qualunque sia il motivo, la gestione delle definizioni del progetto come codice presenta vantaggi. Questo articolo illustra come usare i `Import-AzBlueprintWithArtifact` comandi e `Export-AzBlueprintWithArtifact` nel modulo [AZ. Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) .

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone una conoscenza professionale moderata dei progetti di Azure. Se non è ancora stato fatto, utilizzare gli articoli seguenti:

- [Creare un progetto nel portale](../create-blueprint-portal.md)
- Informazioni sulle [fasi di distribuzione](../concepts/deployment-stages.md) e [il ciclo di vita del progetto](../concepts/lifecycle.md)
- [Creazione](../create-blueprint-powershell.md) e [gestione](./manage-assignments-ps.md) delle definizioni e delle assegnazioni del progetto con PowerShell

Se non è già installato, seguire le istruzioni riportate in [aggiungere il modulo AZ. Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) per installare e convalidare il modulo **AZ. Blueprint** dalla PowerShell Gallery.

## <a name="folder-structure-of-a-blueprint-definition"></a>Struttura di cartelle di una definizione di progetto

Prima di esaminare l'esportazione e l'importazione di progetti, esaminiamo il modo in cui i file che compongono la definizione di progetto sono strutturati. La definizione di un progetto deve essere archiviata nella relativa cartella.

> [!IMPORTANT]
> Se non viene passato alcun valore al parametro **Name** del `Import-AzBlueprintWithArtifact` cmdlet, viene usato il nome della cartella in cui viene archiviata la definizione del progetto.

Insieme alla definizione del progetto, che deve essere denominata `blueprint.json`, sono gli elementi in cui è costituita la definizione del progetto. Ogni artefatto deve trovarsi nella sottocartella `artifacts`denominata.
Insieme, la struttura della definizione del progetto come file JSON nelle cartelle dovrebbe essere simile alla seguente:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Esportare la definizione del progetto

I passaggi per l'esportazione della definizione di progetto sono semplici. L'esportazione della definizione del progetto può essere utile per la condivisione, il backup o l'inserimento nel controllo del codice sorgente.

- **Progetto** necessaria
  - Specifica la definizione del progetto
  - Usare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
- **OutputPath** necessaria
  - Specifica il percorso in cui salvare i file JSON di definizione del progetto
  - I file di output si trovano in una sottocartella con il nome della definizione di progetto
- **Versione** di opzionale
  - Specifica la versione da restituire se l'oggetto di riferimento del **progetto** contiene riferimenti a più di una versione.

1. Ottenere un riferimento alla definizione del progetto da esportare dalla sottoscrizione rappresentata come `{subId}`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Usare il `Export-AzBlueprintWithArtifact` cmdlet per esportare la definizione di progetto specificata:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importare la definizione del progetto

Quando si dispone di una [definizione di progetto esportata](#export-your-blueprint-definition) o di una definizione di progetto creata manualmente nella [struttura di cartelle richiesta](#folder-structure-of-a-blueprint-definition), è possibile importare la definizione del progetto in un gruppo di gestione o in una sottoscrizione diversa.

Per esempi di definizioni di progetto predefinite, vedere il [repository GitHub Azure Blueprint](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Nome** necessaria
  - Specifica il nome della nuova definizione di progetto
- **InputPath** necessaria
  - Specifica il percorso da cui creare la definizione del progetto
  - Deve corrispondere alla [struttura di cartelle richiesta](#folder-structure-of-a-blueprint-definition)
- **Managementgroupid nelle** opzionale
  - ID del gruppo di gestione in cui salvare la definizione del progetto se non l'impostazione predefinita del contesto corrente
  - È necessario specificare **managementgroupid nelle** o **SubscriptionId**
- **SubscriptionId** opzionale
  - ID sottoscrizione in cui salvare la definizione del progetto se non è il contesto corrente predefinito
  - È necessario specificare **managementgroupid nelle** o **SubscriptionId**

1. Usare il `Import-AzBlueprintWithArtifact` cmdlet per importare la definizione di progetto specificata:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Una volta importata la definizione del progetto, [assegnarla a PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Per informazioni sulla creazione di definizioni avanzate del progetto, vedere gli articoli seguenti:

- Utilizzare [parametri statici e dinamici](../concepts/parameters.md).
- Personalizzare l' [ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Proteggi le distribuzioni con il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- [Gestire i progetti come codice](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).