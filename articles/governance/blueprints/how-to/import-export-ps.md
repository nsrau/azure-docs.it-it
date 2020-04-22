---
title: Importare ed esportare blueprint con PowerShellImport and export blueprints with PowerShell
description: Informazioni su come utilizzare le definizioni del blueprint come codice. Condividi, controllo del codice sorgente e gestisci utilizzando i comandi di esportazione e importazione.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686845"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importare ed esportare definizioni di blueprint con PowerShellImport and export blueprint definitions with PowerShell

I blueprint di Azure possono essere completamente gestiti tramite il portale di Azure.Azure Blueprints can be fully managed through Azure portal. Man mano che le organizzazioni avanzano nell'uso dei blueprint di Azure, devono iniziare a pensare alle definizioni dei blueprint come codice gestito. Questo concetto è spesso indicato come infrastruttura come codice (IaC). Trattare le definizioni del blueprint come codice offre ulteriori vantaggi oltre a quelli offerti dal portale di Azure.Treat your blueprint definitions as code offers additional advantages beyond what Azure portal offers. Questi vantaggi includono:

- Condivisione delle definizioni dei blueprint
- Backup delle definizioni dei blueprint
- Riutilizzo delle definizioni di blueprint in tenant o sottoscrizioni diverse
- Inserimento delle definizioni del blueprint nel controllo del codice sorgente
  - Test automatizzato delle definizioni dei blueprint negli ambienti di test
  - Supporto di pipeline di integrazione continua e distribuzione continua (CI/CD)

Qualunque siano le vostre ragioni, la gestione delle definizioni del blueprint come codice presenta vantaggi. In questo articolo viene `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` illustrato come utilizzare i comandi e nel modulo [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone una conoscenza moderata dei blueprint di Azure.This article assumes a moderate working knowledge of Azure Blueprints. Se non l'hai ancora fatto, segui i seguenti articoli:

- [Creare un progetto nel portale](../create-blueprint-portal.md)
- Informazioni sulle fasi di [distribuzione](../concepts/deployment-stages.md) e [sul ciclo di vita del blueprint](../concepts/lifecycle.md)
- [Creazione](../create-blueprint-powershell.md) e [gestione di](./manage-assignments-ps.md) definizioni e assegnazioni di blueprint con PowerShell

Se il modulo **Az.Blueprint** non è già installato, seguire le istruzioni contenute in [Aggiungere il modulo Az.Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) per installarlo e convalidarlo da PowerShell Gallery.

## <a name="folder-structure-of-a-blueprint-definition"></a>Struttura delle cartelle di una definizione di blueprint

Prima di esaminare l'esportazione e l'importazione dei blueprint, esaminiamo come sono strutturati i file che costituiscono la definizione del blueprint. Una definizione di blueprint deve essere archiviata nella propria cartella.

> [!IMPORTANT]
> Se non viene passato **Name** alcun valore `Import-AzBlueprintWithArtifact` al parametro Name del cmdlet, viene utilizzato il nome della cartella in cui è archiviata la definizione del blueprint.

Insieme alla definizione del blueprint, che deve essere denominata `blueprint.json`, sono gli elementi di cui è composta la definizione del blueprint. Ogni elemento deve trovarsi `artifacts`nella sottocartella denominata .
Insieme, la struttura della definizione del blueprint come file JSON nelle cartelle dovrebbe essere la seguente:

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

## <a name="export-your-blueprint-definition"></a>Esportare la definizione del blueprint

I passaggi per esportare la definizione del blueprint sono semplici. L'esportazione della definizione del blueprint può essere utile per la condivisione, il backup o l'inserimento nel controllo del codice sorgente.

- **Blueprint** [obbligatorio]
  - Specifica la definizione del blueprint
  - Utilizzare `Get-AzBlueprint` per ottenere l'oggetto di riferimento
- **OutputPath** [obbligatorio]
  - Specifica il percorso in cui salvare i file JSON di definizione del blueprint
  - I file di output si trovano in una sottocartella con il nome della definizione del blueprint
- **Versione** (opzionale)
  - Specifica la versione da restituire se l'oggetto di riferimento **Blueprint** contiene riferimenti a più versioni.

1. Ottenere un riferimento alla definizione del blueprint `{subId}`da esportare dalla sottoscrizione rappresentata come:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Utilizzare `Export-AzBlueprintWithArtifact` il cmdlet per esportare la definizione del blueprint specificata:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importare la definizione del blueprint

Dopo aver esportato una [definizione di blueprint o](#export-your-blueprint-definition) una definizione di blueprint creata manualmente nella struttura di cartelle [richiesta,](#folder-structure-of-a-blueprint-definition)è possibile importare tale definizione di blueprint in un gruppo di gestione o in una sottoscrizione diversa.

Per esempi di definizioni di blueprint predefinite, vedere il [repository Azure Blueprint GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins).

- **Nome** [obbligatorio]
  - Specifica il nome della nuova definizione di blueprint
- **InputPath** [obbligatorio]
  - Specifica il percorso da cui creare la definizione del blueprint
  - Deve corrispondere alla [struttura di cartelle richiesta](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (facoltativo)
  - ID del gruppo di gestione in cui salvare la definizione del blueprint, se non è il contesto corrente, default
  - È necessario **specificare ManagementGroupId** o **SubscriptionId**
- **SubscriptionId** (facoltativo)
  - Id sottoscrizione in cui salvare la definizione del blueprint se non è il contesto corrente predefinito
  - È necessario **specificare ManagementGroupId** o **SubscriptionId**

1. Utilizzare `Import-AzBlueprintWithArtifact` il cmdlet per importare la definizione del blueprint specificata:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Dopo aver importato la definizione del blueprint, [assegnarla con PowerShell.](./manage-assignments-ps.md#create-blueprint-assignments)

Per informazioni sulla creazione di definizioni di blueprint avanzate, vedere gli articoli seguenti:

- Utilizzare [parametri statici e dinamici.](../concepts/parameters.md)
- Personalizzare [l'ordine di sequenziamento](../concepts/sequencing-order.md)del blueprint.
- Proteggere le distribuzioni con [il blocco delle risorse del blueprint](../concepts/resource-locking.md).
- [Gestisci blueprint come codice](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).