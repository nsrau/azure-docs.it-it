---
title: Panoramica dell'SDK per il runbook grafico di Automazione di Azure
description: Questo articolo descrive come usare l'SDK per il runbook grafico di Automazione di Azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc9f1feff6d8d45e52c8621d1ec4b36e0a4a4a76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61303217"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Usare l'SDK per il runbook grafico di Automazione di Azure

I [runbook grafici](automation-graphical-authoring-intro.md) sono runbook che aiutano a gestire le complessità del codice di Windows PowerShell o del flusso di lavoro PowerShell sottostante. L'SDK di creazione grafica di Automazione di Microsoft Azure consente agli sviluppatori di creare e modificare i runbook grafici per l'uso congiunto con il servizio Automazione di Azure. Gli snippet di codice seguenti mostrano il flusso di base per la creazione di un runbook grafico proveniente da un codice dell'utente.

## <a name="pre-requisites"></a>Prerequisiti

Innanzitutto, importare il pacchetto `Microsoft.Azure.Automation.GraphicalRunbook.Model` nel progetto.

## <a name="create-a-runbook-object-instance"></a>Creare un'istanza di un oggetto runbook

Fare riferimento all'assembly `Orchestrator.GraphRunbook.Model` e creare un'istanza della classe `Orchestrator.GraphRunbook.Model.GraphRunbook`:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Aggiungere parametri di runbook

Creare un'istanza degli oggetti `Orchestrator.GraphRunbook.Model.Parameter` e aggiungerla al runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Aggiungere attività e collegamenti

Creare un'istanza di attività dei tipi appropriati e aggiungerla al runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Le attività vengono implementate dalle classi seguenti nello spazio dei nomi `Orchestrator.GraphRunbook.Model`:

|Classe  |Attività  |
|---------|---------|
|CommandActivity     | Richiama un comando di PowerShell (cmdlet, funzioni e così via).        |
|InvokeRunbookActivity     | Richiama un altro runbook inline.        |
|JunctionActivity     | Attende il completamento di tutti i rami in ingresso.        |
|WorkflowScriptActivity     | Esegue un blocco di codice PowerShell o del flusso di lavoro di PowerShell (a seconda del tipo di runbook) nel contesto del runbook. Questo è uno strumento potente, ma è necessario non abusarne: l'interfaccia utente visualizzerà questo blocco di script come testo, il motore di esecuzione considererà il blocco specificato come una scatola nera e non farà alcun tentativo di analizzare il contenuto, ad eccezione di un controllo della sintassi di base. Se è necessario richiamare solo un singolo comando di PowerShell, preferire CommandActivity.        |

> [!NOTE]
> Non far derivare le proprie attività personalizzate dalle classi fornite: Automazione di Azure non sarà in grado di usare i runbook con i tipi di attività personalizzate.

È necessario specificare i parametri CommandActivity e InvokeRunbookActivity come i descrittori di valore, non come valori diretti. I descrittori di valore specificano come devono essere generati i valori di parametro effettivi. I descrittori di valore seguenti sono attualmente disponibili:


|Descrittore  |Definizione  |
|---------|---------|
|ConstantValueDescriptor     | Fa riferimento a un valore costante a livello di codice.        |
|RunbookParameterValueDescriptor     | Fa riferimento a un parametro del runbook in base al nome.        |
|ActivityOutputValueDescriptor     | Fa riferimento a un'attività upstream di output, che consente a un'attività di "sottoscriversi" ai dati prodotti da un'altra attività.        |
|AutomationVariableValueDescriptor     | Fa riferimento a un asset della variabile di automazione in base al nome.         |
|AutomationCredentialValueDescriptor     | Fa riferimento a un asset del certificato di automazione in base al nome.        |
|AutomationConnectionValueDescriptor     | Fa riferimento a un asset della connessione di automazione in base al nome.        |
|PowerShellExpressionValueDescriptor     | Specifica un'espressione di PowerShell in formato libero che verrà valutata poco prima di richiamare l'attività.  <br/>Questo è uno strumento potente, ma è necessario non abusarne: l'interfaccia utente visualizzerà questa espressione come testo, il motore di esecuzione considererà il blocco specificato come una scatola nera e non farà alcun tentativo di analizzare il contenuto, ad eccezione di un controllo della sintassi di base. Quando possibile, preferire i descrittori di valore più specifici.      |

> [!NOTE]
> Non far derivare i propri descrittori di valore dalle classi fornite: Automazione di Azure non sarà in grado di usare i runbook con i tipi di descrittori di valore personalizzati.

Creare un'istanza di collegamenti collegando le attività e aggiungerla al runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Salvare il runbook in un file

Usare `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` per serializzare un runbook in una stringa:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Questa stringa può essere salvata in un file con l'estensione **.graphrunbook** e questo file può essere importato in Automazione di Azure.
Il formato serializzato può cambiare in futuro le versioni di `Orchestrator.GraphRunbook.Model.dll`. Si garantisce la compatibilità con le versioni precedenti: qualsiasi runbook serializzato con una versione precedente di `Orchestrator.GraphRunbook.Model.dll` può essere deserializzato da qualsiasi versione più recente. La compatibilità con le versioni future non è garantita: un runbook serializzato con una versione più recente potrebbe non essere deserializzabile dalle versioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui runbook grafici in Automazione di Azure vedere [Introduzione alla creazione grafica](automation-graphical-authoring-intro.md)

