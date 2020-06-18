---
title: Usare l'SDK per il runbook grafico di Automazione di Azure (anteprima)
description: Questo articolo illustra come usare l'SDK per il runbook grafico di Automazione di Azure (anteprima).
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 969e60cd08a65adb1dd731aa7c6c3f9872e288fd
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835037"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk-preview"></a>Usare l'SDK per il runbook grafico di Automazione di Azure (anteprima)

I [runbook grafici](automation-graphical-authoring-intro.md) aiutano a gestire le complessità del codice di Windows PowerShell o del flusso di lavoro PowerShell sottostante. L'SDK di creazione grafica di Automazione di Microsoft Azure consente agli sviluppatori di creare e modificare i runbook grafici per l'uso congiunto con Automazione di Azure. Questo articolo descrive i passaggi di base per la creazione di un runbook grafico dal codice.

## <a name="prerequisites"></a>Prerequisiti

Per importare il pacchetto `Orchestrator.GraphRunbook.Model.dll`, scaricare l'[SDK](https://www.microsoft.com/download/details.aspx?id=50734).

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

Le attività vengono implementate dalle classi seguenti nello spazio dei nomi `Orchestrator.GraphRunbook.Model`.

|Classe  |Attività  |
|---------|---------|
|CommandActivity     | Richiama un comando di PowerShell (cmdlet, funzioni e così via).        |
|InvokeRunbookActivity     | Richiama un altro runbook inline.        |
|JunctionActivity     | Attende il completamento di tutti i rami in ingresso.        |
|WorkflowScriptActivity     | Esegue un blocco di codice PowerShell o del flusso di lavoro di PowerShell (a seconda del tipo di runbook) nel contesto del runbook. Questo è uno strumento potente, ma è necessario non abusarne: l'interfaccia utente visualizzerà questo blocco di script come testo, il motore di esecuzione considererà il blocco specificato come una scatola nera e non farà alcun tentativo di analizzare il contenuto, ad eccezione di un controllo della sintassi di base. Se è necessario richiamare solo un singolo comando di PowerShell, preferire CommandActivity.        |

> [!NOTE]
> Non far derivare le proprie attività personalizzate dalle classi fornite. Automazione di Azure non può usare i runbook con i tipi di attività personalizzate.

È necessario specificare i parametri `CommandActivity` e `InvokeRunbookActivity` come descrittori di valore, non come valori diretti. I descrittori di valore specificano come generare i valori di parametro effettivi. I descrittori di valore seguenti sono attualmente disponibili:


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
> Non far derivare i propri descrittori di valore dalle classi fornite. Automazione di Azure non può usare i runbook con i tipi di descrittori di valore personalizzati.

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

È possibile salvare questa stringa in un file con l'estensione **graphrunbook**. Il runbook corrispondente può essere importato in Automazione di Azure.
Il formato serializzato potrebbe cambiare in futuro le versioni di `Orchestrator.GraphRunbook.Model.dll`. Si garantisce la compatibilità con le versioni precedenti: qualsiasi runbook serializzato con una versione precedente di `Orchestrator.GraphRunbook.Model.dll` può essere deserializzato da qualsiasi versione più recente. La compatibilità con le versioni future non è garantita: un runbook serializzato con una versione più recente potrebbe non essere deserializzabile dalle versioni precedenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Creare runbook grafici in Automazione di Azure](automation-graphical-authoring-intro.md).