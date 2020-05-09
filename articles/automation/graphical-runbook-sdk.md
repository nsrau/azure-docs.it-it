---
title: Usare Azure Automation Runbook Graphical SDK
description: Questo articolo descrive come usare Azure Automation Graphical Runbook SDK.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 886ce03b6e107d871879ff40bdc5de9ceb97c7c3
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690746"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Usare Azure Automation Runbook Graphical SDK

[Manuali operativi grafici](automation-graphical-authoring-intro.md) consentono di gestire le complessità del codice del flusso di lavoro di Windows PowerShell o PowerShell sottostante. Il Microsoft Azure SDK per la creazione grafica di automazione consente agli sviluppatori di creare e modificare manuali operativi grafici per l'uso con automazione di Azure. Questo articolo descrive i passaggi di base per la creazione di un Runbook grafico dal codice.

## <a name="prerequisites"></a>Prerequisiti

Importare il `Orchestrator.GraphRunbook.Model.dll` pacchetto nel progetto.

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

Le attività vengono implementate dalle classi seguenti nello `Orchestrator.GraphRunbook.Model` spazio dei nomi.

|Classe  |Attività  |
|---------|---------|
|CommandActivity     | Richiama un comando di PowerShell (cmdlet, funzioni e così via).        |
|InvokeRunbookActivity     | Richiama un altro runbook inline.        |
|JunctionActivity     | Attende il completamento di tutti i rami in ingresso.        |
|WorkflowScriptActivity     | Esegue un blocco di codice PowerShell o del flusso di lavoro di PowerShell (a seconda del tipo di runbook) nel contesto del runbook. Questo è uno strumento potente, ma è necessario non abusarne: l'interfaccia utente visualizzerà questo blocco di script come testo, il motore di esecuzione considererà il blocco specificato come una scatola nera e non farà alcun tentativo di analizzare il contenuto, ad eccezione di un controllo della sintassi di base. Se è necessario richiamare solo un singolo comando di PowerShell, preferire CommandActivity.        |

> [!NOTE]
> Non derivare le proprie attività dalle classi fornite. Automazione di Azure non può usare manuali operativi con i tipi di attività personalizzati.

È necessario specificare `CommandActivity` i `InvokeRunbookActivity` parametri e come descrittori di valore, non i valori diretti. I descrittori di valore specificano come produrre i valori di parametro effettivi. I descrittori di valore seguenti sono attualmente disponibili:


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
> Non derivare i propri descrittori di valore dalle classi fornite. Automazione di Azure non può usare manuali operativi con tipi di descrittori di valore personalizzati.

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

È possibile salvare questa stringa in un file con estensione **graphrunbook** . Il Runbook corrispondente può essere importato in automazione di Azure.
Il formato serializzato potrebbe cambiare nelle versioni future di `Orchestrator.GraphRunbook.Model.dll`. Si garantisce la compatibilità con le versioni precedenti: qualsiasi runbook serializzato con una versione precedente di `Orchestrator.GraphRunbook.Model.dll` può essere deserializzato da qualsiasi versione più recente. La compatibilità con le versioni future non è garantita: un runbook serializzato con una versione più recente potrebbe non essere deserializzabile dalle versioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui manuali operativi grafici in automazione di Azure, vedere [Introduzione alla creazione grafica](automation-graphical-authoring-intro.md).