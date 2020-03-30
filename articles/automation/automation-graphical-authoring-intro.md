---
title: Creazione grafica in Automazione di Azure
description: La creazione grafica consente di creare Runbook per Automazione di Azure senza usare codice. Questo articolo offre un'introduzione alla creazione grafica e descrive tutti i dettagli necessari per iniziare la creazione di un Runbook grafico.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 09afca7eaf385795baf9c4a3c94232622527e357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500984"
---
# <a name="graphical-authoring-in-azure-automation"></a>Creazione grafica in Automazione di Azure

La creazione grafica consente di creare runbook per l'automazione di Azure senza la complessità del codice del flusso di lavoro di Windows PowerShell o PowerShell sottostante. È possibile aggiungere attività all'area di disegno da una raccolta di cmdlet e runbook, collegarli e configurarli per formare un flusso di lavoro. Se hai mai lavorato con System Center Orchestrator o Service Management Automation (SMA), la creazione grafica dovrebbe avere un aspetto familiare. Questo articolo fornisce un'introduzione ai concetti necessari per iniziare a creare un runbook grafico.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Runbook grafici

Tutti i runbook in Automazione di Azure sono flussi di lavoro di Windows PowerShell.All runbooks in Azure Automation are Windows PowerShell workflows. I runbook grafici e i runbook grafici del flusso di lavoro di PowerShell generano codice PowerShell eseguito dai lavoratori di automazione ma che non è possibile visualizzare o modificare. È possibile convertire un runbook grafico in un runbook grafico del flusso di lavoro di PowerShell e viceversa. Tuttavia, non è possibile convertire questi runbook in un runbook testuale. Inoltre, l'editor grafico di automazione non può importare un runbook testuale.

## <a name="overview-of-graphical-editor"></a>Panoramica dell'editor grafico

È possibile aprire l'editor grafico nel portale di Azure creando o modificando un Runbook grafico.

![Area di lavoro grafica](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Le sezioni seguenti descrivono i controlli dell'editor grafico.

### <a name="canvas-control"></a>Canvas (controllo)

Il controllo Canvas consente di progettare il runbook. È possibile aggiungere attività dai nodi nel controllo Libreria al runbook e connetterle con collegamenti per definire la logica del runbook. Nella parte inferiore dell'area di disegno sono disponibili controlli che consentono di eseguire lo zoom avanti e indietro.

### <a name="library-control"></a>Controllo Library

Il controllo Libreria consente di selezionare [le attività](#activities) da aggiungere al runbook. È possibile aggiungerli all'area di disegno, dove è possibile connetterli ad altre attività. Il controllo Libreria include le sezioni definite nella tabella seguente.

| Sezione | Descrizione |
|:--- |:--- |
| Cmdlet |Tutti i cmdlet che possono essere utilizzati nel runbook. I cmdlet sono organizzati per modulo. Sono disponibili tutti i moduli installati nell'account Automation. |
| Runbook |I runbook nell'account di automazione. Puoi aggiungere questi runbook all'area di disegno per essere usati come runbook figlio. Vengono visualizzati solo i runbook dello stesso tipo principale del runbook in fase di modifica. Per i runbook grafici, vengono visualizzati solo i runbook basati su PowerShell.For graphical runbooks, only PowerShell-based runbooks are shown. Per i runbook grafici del flusso di lavoro di PowerShell, vengono visualizzati solo i runbook basati sul flusso di lavoro di PowerShell.For graphical PowerShell Workflow runbooks, only PowerShell Workflow-based runbooks are shown. |
| Asset |Le [risorse di automazione](/previous-versions/azure/dn939988(v=azure.100)) nell'account Di automazione che puoi usare nel tuo runbook. L'aggiunta di una risorsa a un runbook aggiunge un'attività del flusso di lavoro che ottiene l'asset selezionato. Nel caso di attività di variabile, è possibile selezionare se aggiungere un'attività per ottenere la variabile o per impostarla. |
| Runbook Control |Controllare le attività che possono essere utilizzate nel runbook corrente. Un'attività Junction accetta più input e attende fino al completamento di tutti prima di continuare il flusso di lavoro. Un'attività Codice esegue una o più righe di codice del flusso di lavoro di PowerShell o PowerShell, a seconda del tipo di runbook grafico. È possibile usare questa attività per il codice personalizzato o per funzionalità difficili da ottenere con altre attività. |

### <a name="configuration-control"></a>Controllo Configuration

Il controllo Configurazione consente di fornire dettagli per un oggetto selezionato nell'area di disegno. Le proprietà disponibili in questo controllo dipendono dal tipo di oggetto selezionato. Quando si sceglie un'opzione nel controllo Configurazione, vengono visualizzati pannelli aggiuntivi per fornire ulteriori informazioni.

### <a name="test-control"></a>Controllo Test

Il controllo Test non viene visualizzato al primo avvio dell'editor grafico. Viene aperto quando si testa un Runbook graficoin modo interattivo.

## <a name="activities"></a>attività

Le attività sono i blocchi predefiniti di un Runbook. Un'attività può essere un cmdlet di PowerShell, un runbook figlio o un flusso di lavoro. È possibile aggiungere un'attività al runbook facendo clic con il pulsante destro del mouse sul controllo Libreria e scegliendo **Aggiungi all'area di disegno**. È quindi possibile fare clic e trascinare l'attività per posizionarla in un punto qualsiasi nel canvas desiderato. La posizione dell'attività nell'area di disegno non influisce sul funzionamento del runbook. È possibile disporre il runbook in qualsiasi modo si trova più adatto per visualizzare il suo funzionamento.

![Add to canvas](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selezionare un'attività nell'area di disegno per configurarne le proprietà e i parametri nel pannello Configurazione.Select an activity on the canvas to configure its properties and parameters in the Configuration blade. È possibile modificare l'etichetta dell'attività con un nome descrittivo. Il runbook continua a eseguire il cmdlet originale. Si sta semplicemente cambiando il nome visualizzato utilizzato dall'editor grafico. Si noti che l'etichetta deve essere univoca all'interno del runbook.

### <a name="parameter-sets"></a>Set di parametri

Un set di parametri definisce i parametri obbligatori e facoltativi che accetteranno i valori per un cmdlet specifico. Tutti i cmdlet dispongono di almeno un set di parametri e alcuni dispongono di diversi set. Se un cmdlet dispone di più set di parametri, è necessario selezionare quello da utilizzare prima di poter configurare i parametri. È possibile modificare il set di parametri utilizzato da un'attività selezionando **Set di** parametri e scegliendo un altro set. In questo caso, tutti i valori dei parametri già configurati vengono persi.

Nell'esempio seguente, il cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) dispone di tre set di parametri. Nell'esempio viene utilizzato un set denominato **ListVirtualMachineInResourceGroupParamSet**, con un singolo parametro facoltativo, per la restituzione di tutte le macchine virtuali in un gruppo di risorse. Nell'esempio viene inoltre utilizzato il set di parametri **GetVirtualMachineInResourceGroupParamSet** per specificare la macchina virtuale da restituire. Questo set ha due parametri obbligatori e un parametro facoltativo.

![Parameter Set](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valori dei parametri

Quando si specifica un valore per un parametro, è necessario selezionare un'origine dati per determinare come verrà specificato il valore. Le origini dati disponibili per un determinato parametro dipendono dai valori validi per tale parametro. Ad esempio, Null non sarà disponibile come opzione di un parametro che non consente valori Null.

| origine dati | Descrizione |
|:--- |:--- |
| Constant Value |Digitare un valore per il parametro. Questa origine dati è disponibile solo per i seguenti tipi di dati: Int32, Int64, String, Boolean, DateTime, Switch. |
| Activity Output |Usare l'output di un'attività che precede l'attività corrente nel flusso di lavoro. Verranno elencate tutte le attività valide. Per il valore del parametro, utilizzare solo l'attività che produce l'output. Se l'attività restituisce un oggetto con più proprietà, è possibile digitare il nome di una proprietà specifica dopo aver selezionato l'attività. |
| Input di runbook |Selezionare un input del runbook come input per il parametro dell'attività. |
| Asset della variabile |Selezionare una variabile di automazione come input. |
| Asset di credenziali |Selezionare una credenziale di automazione come input. |
| Asset del certificato |Selezionare un certificato di automazione come input. |
| Asset di connessione |Selezionare una connessione di automazione come input. |
| PowerShell Expression |Specificare [un'espressione di PowerShell](#powershell-expressions)semplice. L'espressione viene valutata prima dell'attività e il risultato viene utilizzato per il valore del parametro. È possibile usare variabili per fare riferimento all'output di un'attività o un parametro di input di Runbook. |
| Non configurato |Cancellare qualsiasi valore configurato in precedenza. |

#### <a name="optional-additional-parameters"></a>Parametri aggiuntivi facoltativi

Per tutti i cmdlet è possibile specificare parametri aggiuntivi. Si tratta di parametri comuni di PowerShell o di altri parametri personalizzati. L'editor grafico presenta una casella di testo in cui è possibile fornire parametri usando la sintassi di PowerShell.The graphical editor presents a text box where you can provide parameters using PowerShell syntax. Ad esempio, per `Verbose` utilizzare il parametro common, è necessario specificare `-Verbose:$True`.

### <a name="retry-activity"></a>Ripetere l'attività

La funzionalità di ripetizione dei tentativi per un'attività ne consente l'esecuzione più volte finché non viene soddisfatta una determinata condizione, in modo analogo a un ciclo. È possibile utilizzare questa funzionalità per le attività che devono essere eseguite più volte, sono soggette a errori, potrebbero essere necessarie più di un tentativo di esito positivo o testare le informazioni di output dell'attività per i dati validi.

Quando si abilita la ripetizione dei tentativi per un'attività, è possibile impostare un ritardo e una condizione. Il ritardo è il tempo, espresso in secondi o minuti, che il runbook attenderà prima di eseguire nuovamente l'attività. Se non si specifica un ritardo, l'attività viene eseguita nuovamente immediatamente dopo il completamento.

![Ritardo di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/retry-delay.png)

La condizione di ripetizione dei tentativi è un'espressione di PowerShell che viene valutata dopo ogni esecuzione dell'attività. Se l'espressione viene risolta in True, l'attività viene eseguita nuovamente. Se l'espressione viene risolta in False, l'attività non viene eseguita nuovamente e il runbook passa all'attività successiva.

![Ritardo di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/retry-condition.png)

La condizione di ripetizione `RetryData` dei tentativi può usare una variabile denominata che fornisce l'accesso alle informazioni sui tentativi di attività. Questa variabile include le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| `NumberOfAttempts` |Numero di volte in cui l'attività è stata eseguita. |
| `Output` |Output dell'ultima esecuzione dell'attività. |
| `TotalDuration` |Tempo trascorso dal primo avvio dell'attività. |
| `StartedAt` |Ora (in formato UTC) quando l'attività è stata avviata per la prima volta. |

Di seguito sono riportati esempi di condizioni di ripetizione dei tentativi di attività.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Dopo aver configurato la condizione di ripetizione dei tentativi per un'attività, l'attività include due segnali visivi come promemoria per l'utente. Uno viene presentato nell'attività e l'altro viene visualizzato quando si esamina la configurazione dell'attività.

![Segnali visivi di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controllo Workflow Script

Un controllo Script del flusso di lavoro è un'attività speciale che accetta script di PowerShell o del flusso di lavoro di PowerShell, a seconda del tipo di runbook grafico creato. Questo controllo fornisce funzionalità che potrebbero non essere disponibili con altri mezzi. Non può accettare parametri, ma può usare le variabili per parametri di output di attività e di input di Runbook. Qualsiasi output dell'attività viene aggiunto al databus. Un'eccezione è l'output senza collegamento in uscita, nel qual caso l'output viene aggiunto all'output del runbook.

Ad esempio, il codice seguente esegue calcoli di `NumberOfDays`data utilizzando una variabile di input del runbook denominata . Invia quindi un valore DateTime calcolato come output da utilizzare per le attività successive nel runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Collegamenti e flusso di lavoro

Un collegamento in un Runbook grafico connette due attività. Viene visualizzato nel canvas come una freccia che punta dall'attività di origine verso l'attività di destinazione. Le attività vengono eseguite nella direzione della freccia, con l'attività di destinazione che inizia dopo il completamento dell'attività di origine.

### <a name="link-creation"></a>Creazione di collegamenti

È possibile creare un collegamento tra due attività selezionando l'attività di origine e facendo clic sul cerchio nella parte inferiore della forma. Trascinare la freccia sull'attività di destinazione e rilasciare.

![Creare un collegamento](media/automation-graphical-authoring-intro/create-link-options.png)

Selezionare il collegamento per configurare le relative proprietà nel pannello Configuration, Le proprietà includono il tipo di collegamento, descritto nella tabella seguente.

| Tipo di collegamento | Descrizione |
|:--- |:--- |
| Pipeline |L'attività di destinazione viene eseguita una volta per ogni output dell'oggetto dall'attività di origine. L'attività di destinazione non viene eseguita se l'attività di origine non genera alcun output. L'output dell'attività di origine è disponibile come oggetto. |
| Sequenza |L'attività di destinazione viene eseguita una sola volta quando riceve l'output dall'attività di origine. L'output dell'attività di origine è disponibile come matrice di oggetti. |

### <a name="start-of-activity"></a>Inizio dell'attività

Un runbook grafico inizia con le attività che non dispongono di un collegamento in ingresso. Spesso esiste una sola attività che funge da attività di partenza per il runbook. Se più attività non dispongono di un collegamento in ingresso, il runbook inizia eseguendole in parallelo. Seguirà i collegamenti per eseguire altre attività man mano che vengono completate.

### <a name="link-conditions"></a>Condizioni di collegamento

Quando si specifica una condizione su un collegamento, l'attività di destinazione viene eseguita solo se la condizione viene risolta in True. In genere `ActivityOutput` si usa una variabile in una condizione per recuperare l'output dall'attività di origine.

Per un collegamento pipeline, è necessario specificare una condizione per un singolo oggetto. Il runbook valuta la condizione per ogni output dell'oggetto in base all'attività di origine. Viene quindi eseguita l'attività di destinazione per ogni oggetto che soddisfa la condizione. Ad esempio, con un'attività di origine di `Get-AzVM`, è possibile utilizzare la sintassi seguente per un collegamento alla pipeline condizionale per recuperare solo le macchine virtuali nel gruppo di risorse denominato Gruppo1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Per un collegamento di sequenza, il runbook valuta la condizione una sola volta, poiché viene restituita una singola matrice contenente tutti gli oggetti dell'attività di origine. Per questo motivo, il runbook non può usare un collegamento di sequenza per il filtro, come è possibile con un collegamento alla pipeline. Il collegamento di sequenza può semplicemente determinare se l'attività successiva viene eseguita o meno.

Ad esempio, prendere il set di attività seguente nel runbook **Start VM:For** example, take the following set of activities in our Start VM runbook:

![Collegamento condizionale con sequenze](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Il runbook utilizza tre diversi collegamenti di `VMName` sequenza che verificano i valori dei parametri di input e `ResourceGroupName` per determinare l'azione appropriata da eseguire. Le azioni possibili sono avviare una singola macchina virtuale, avviare tutte le macchine virtuali nel gruppo di risorse o avviare tutte le macchine virtuali in una sottoscrizione. Per il collegamento `Connect to Azure` `Get single VM`di sequenza tra e , ecco la logica della condizione:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Quando si usa un collegamento condizionale, i dati disponibili dall'attività di origine per altre attività nel ramo vengono filtrati dalla condizione. Se un'attività è l'origine di più collegamenti, i dati disponibili per le attività in ogni ramo dipendono dalla condizione nel collegamento che si connette a tale ramo.

Ad esempio, `Start-AzVM` l'attività nel runbook seguente avvia tutte le macchine virtuali. Dispone di due collegamenti condizionali. Il primo collegamento condizionale `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` utilizza l'espressione per filtrare se l'attività `Start-AzVM` viene completata correttamente. Il secondo collegamento condizionale `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` usa l'espressione per filtrare se l'attività `Start-AzVm` non riesce ad avviare la macchina virtuale.

![Esempio di collegamento condizionale](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualsiasi attività che segue il primo collegamento `Get-AzureVM` e usa l'output dell'attività `Get-AzureVM` da solo recupera le macchine virtuali avviate al momento dell'esecuzione. Qualsiasi attività che segue il secondo collegamento ottiene solo le `Get-AzureVM` macchine virtuali che sono state arrestate al momento dell'esecuzione. Le attività che seguono il terzo collegamento recuperano tutte le macchine virtuali, indipendentemente dallo stato di esecuzione.

### <a name="junctions"></a>Giunzioni

Una giunzione è un'attività speciale che attende il completamento di tutti i rami in ingresso. Ciò consente al runbook di eseguire più attività in parallelo e assicurarsi che tutte siano state completate prima di procedere.

Mentre un nodo può avere un numero illimitato di collegamenti in ingresso, solo uno di questi collegamenti può essere una pipeline. Il numero di collegamenti di tipo sequenza in ingresso non prevede vincoli. È possibile creare il nodo con più collegamenti pipeline in ingresso e salvare il runbook, ma avrà esito negativo quando viene eseguito.

L'esempio seguente fa parte di un Runbook che avvia un set di macchine virtuali e scarica contemporaneamente patch da applicare a tali macchine. Utilizza un nodo per garantire che entrambi i processi vengano completati prima che il runbook continui.

![giunzione](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cicli

Un ciclo si forma quando un'attività di destinazione si collega alla relativa attività di origine o a un'altra attività che alla fine si collega all'origine. L'authoring grafico attualmente non supporta i cicli. Se un runbook dispone di un ciclo, verrà salvato correttamente, ma riceverà un errore durante l'esecuzione.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Condivisione dei dati tra le attività

Tutti i dati restituiti da un'attività con un collegamento in uscita vengono scritti nel databus per il runbook. Le attività nel Runbook possono usare i dati del bus di dati per popolare valori di parametri o includere codice di script. Un'attività può accedere all'output di qualsiasi attività precedente nel flusso di lavoro.

I dati vengono scritti nel bus di dati secondo il tipo di collegamento dell'attività. Per un collegamento alla pipeline, i dati vengono restituiti come più oggetti. Per una sequenza , i dati vengono restituiti come matrice. Se è presente un solo valore, viene restituito come matrice a elemento singolo.

Il runbook ha due modi per accedere ai dati sul databus: 
* Usare un'origine dati di output dell'attività.
* Usare un'origine dati di espressioni di PowerShell.Use a PowerShell expression data source.

Il primo meccanismo usa un'origine dati di output dell'attività per popolare un parametro di un'altra attività. Se l'output è un oggetto, il runbook può specificare una singola proprietà.

![uscita di attività](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Il secondo meccanismo di accesso ai dati recupera l'output di un'attività in un'origine dati di espressioni di PowerShell o un'attività di script del flusso di lavoro con una `ActivityOutput` variabile, usando la sintassi illustrata di seguito. Se l'output è un oggetto, il runbook può specificare una singola proprietà.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Checkpoint

È possibile impostare [i checkpoint](automation-powershell-workflow.md#checkpoints) in un runbook grafico del flusso di lavoro di PowerShell selezionando **Runbook Checkpoint** in qualsiasi attività. In questo modo viene impostato un checkpoint dopo l'esecuzione dell'attività.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

I checkpoint sono abilitati solo nei runbook grafici del flusso di lavoro di PowerShell e non sono disponibili nei runbook grafici. Se il runbook usa i cmdlet di Azure, `Connect-AzAccount` deve seguire qualsiasi attività con checkpoint con un'attività. L'operazione di connessione viene utilizzata nel caso in cui il runbook venga sospeso e debba riavviare da questo checkpoint su un worker diverso.

## <a name="runbook-input-and-output"></a>Input e output di Runbook

### <a name="runbook-input"></a>Input runbook<a name="runbook-input"></a>

Un runbook richiede l'input da un utente che avvia il runbook tramite il portale di Azure o da un altro runbook, se quello corrente viene usato come figlio. Ad esempio, per un runbook che crea una macchina virtuale, l'utente potrebbe dover fornire informazioni quali il nome della macchina virtuale e altre proprietà ogni volta che viene avviato il runbook.

Il runbook accetta l'input definendo uno o più parametri di input. L'utente fornisce i valori per questi parametri a ogni avvio del runbook. Quando l'utente avvia il runbook usando il portale di Azure, all'utente viene richiesto di fornire valori per ogni parametro di input supportato dal runbook.

Quando si crea il runbook, è possibile accedere ai relativi parametri di input facendo clic su **Input e output** sulla barra degli strumenti del runbook. Verrà aperto il controllo Input e output, in cui è possibile modificare un parametro di input esistente o crearne uno nuovo facendo clic su **Aggiungi input**.

![Add input](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Ogni parametro di input è definito dalle proprietà descritte nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| Nome | Obbligatorio. Nome del parametro. Il nome deve essere univoco all'interno del runbook. Deve iniziare con una lettera e può contenere solo lettere, numeri e caratteri di sottolineatura. Il nome non può contenere uno spazio. |
| Descrizione |Facoltativa. Descrizione dello scopo del parametro di input. |
| Type | Facoltativa. Tipo di dati previsto per il valore del parametro. Il portale di Azure visualizzerà un controllo appropriato per il tipo di dati per ogni parametro quando viene richiesto l'input. I tipi di parametro supportati sono String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se non è selezionato un tipo di dati, l'impostazione predefinita è String.|
| Obbligatorio | Facoltativa. Impostazione che specifica se è necessario specificare un valore per il parametro. Se si `yes`sceglie , è necessario specificare un valore all'avvio del runbook. Se si `no`sceglie , un valore non è necessario all'avvio del runbook ed è possibile utilizzare un valore predefinito. Il runbook non può essere avviato se non si specifica un valore per ogni parametro obbligatorio che non dispone di un valore predefinito definito. |
| Default Value | Facoltativa. Valore utilizzato per un parametro se non ne viene passato uno all'avvio del runbook. Per impostare un `Custom`valore predefinito, scegliere . Selezionare `None` se non si desidera fornire alcun valore predefinito. |

### <a name="runbook-output"></a>Output del Runbook

La creazione grafica salva i dati creati da qualsiasi attività che non dispone di un collegamento in uscita [all'output del runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). L'output viene salvato con il processo di Runbook ed è disponibile per un Runbook padre quando il Runbook viene usato come Runbook figlio.

## <a name="powershell-expressions"></a>Espressioni PowerShell

Uno dei vantaggi della creazione grafica è che consente di creare un runbook con una conoscenza minima di PowerShell.One of the advantages of graphical authoring is that it allows you to build a runbook with minimal knowledge of PowerShell. Attualmente, tuttavia, è necessario conoscere un po' di PowerShell per popolare determinati valori di [parametro](#activities) e per l'impostazione di [condizioni](#links-and-workflow)di collegamento . In questa sezione viene fornita una rapida introduzione alle espressioni di PowerShell.This section provides a quick introduction to PowerShell expressions. I dettagli completi di PowerShell sono disponibili all'indirizzo [Scripting con Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Origine dati delle espressioni PowerShell

È possibile usare un'espressione di PowerShell come origine dati per popolare il valore di un [parametro](#activities) di attività con i risultati del codice di PowerShell.You can use a PowerShell expression as a data source to populate the value of an activity parameter with the results of PowerShell code. L'espressione può essere una singola riga di codice che esegue una funzione semplice o più righe che eseguono una logica complessa. L'output di un comando che non è assegnato a una variabile è l'output per il valore del parametro.

Ad esempio, il comando seguente restituisce la data corrente.

```powershell-interactive
Get-Date
```

Il frammento di codice successivo compila una stringa dalla data corrente e la assegna a una variabile. Il codice invia il contenuto della variabile all'output.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

I comandi seguenti valutano la data corrente e restituiscono una stringa che indica se il giorno corrente è un fine settimana o un giorno della settimana.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Activity Output

Per usare l'output di un'attività precedente `ActivityOutput` nel runbook, usare la variabile con la sintassi seguente.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Ad esempio, è possibile avere un'attività con una proprietà che richiede il nome di una macchina virtuale. In questo caso, il runbook può usare l'espressione seguente.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se la proprietà richiede l'oggetto macchina virtuale anziché solo un nome, il runbook restituisce l'intero oggetto utilizzando la sintassi seguente.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Il runbook può usare l'output di un'attività in un'espressione più complessa, ad esempio: Questa espressione concatena il testo al nome della macchina virtuale.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Condizioni

Utilizzare [operatori di confronto](https://technet.microsoft.com/library/hh847759.aspx) per confrontare i valori o determinare se un valore corrisponde a un modello specificato. Un confronto restituisce un valore True o False.

Ad esempio, la condizione seguente determina se `Get-AzureVM` la macchina virtuale da un'attività denominata è attualmente arrestata.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

La condizione seguente determina se la stessa macchina virtuale si trova in uno stato diverso da quello arrestato.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

È possibile unire più condizioni nel runbook `-and` utilizzando `-or`un [operatore logico](https://technet.microsoft.com/library/hh847789.aspx), ad esempio o . Ad esempio, la condizione seguente controlla se la macchina virtuale nell'esempio precedente si trova in uno stato di arresto o arresto.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelle hash

[Le tabelle hash](https://technet.microsoft.com/library/hh847780.aspx) sono coppie nome-valore utili per restituire un set di valori. È inoltre possibile visualizzare una tabella hash denominata dizionario. Le proprietà per determinate attività prevedono una tabella hash anziché un valore semplice.

Creare una tabella hash utilizzando la sintassi seguente. Può contenere un numero qualsiasi di voci, ma ognuna è definita da un nome e un valore.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Ad esempio, l'espressione seguente crea una tabella hash da utilizzare come origine dati per un parametro di attività che prevede una tabella hash di valori per una ricerca su Internet.For example, the following expression creates a hashtable to be used as the data source for an activity parameter that expects a hashtable of values for an internet search.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Nell'esempio seguente viene utilizzato `Get Twitter Connection` l'output di un'attività chiamata per popolare una tabella hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Autenticazione per le risorse di Azure

La maggior parte dei runbook di Automazione di Azure che gestisce risorse di Azure richiede l'autenticazione ad Azure. [L'account RunAs](automation-create-runas-account.md), noto anche come entità servizio, è il meccanismo predefinito utilizzato da un runbook di Automazione per accedere alle risorse di Azure Resource Manager nella sottoscrizione. È possibile aggiungere questa funzionalità a un `AzureRunAsConnection` runbook grafico aggiungendo l'asset di connessione, che usa il cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) di PowerShell, all'area di disegno. È inoltre possibile aggiungere il cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Questo scenario è illustrato nell'esempio seguente.

![Attività di autenticazione RunAs](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection` L'attività, `Get-AutomationConnection`oppure , è configurata `AzureRunAsConnection`con un'origine dati con valori costanti denominata .

![Configurazione della connessione RunAs](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

L'attività `Connect-AzAccount`successiva, , aggiunge l'account RunAs autenticato da utilizzare nel runbook.

![Set di parametri Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Per i runbook `Add-AzAccount` `Add-AzureRMAccount` di PowerShell `Connect-AzAccount`e sono alias per . Si noti che questi alias non sono disponibili per i runbook grafici. Un runbook grafico `Connect-AzAccount` può usare solo se stesso.

Per i campi parametro **APPLICATIONID**, **CERTIFICATETHUMBPRINT**e **TENANTID**, specificare il nome della proprietà per il percorso Field, poiché l'attività restituisce un oggetto con più proprietà. In caso contrario, quando viene eseguito il runbook, non riesce durante il tentativo di autenticazione. Questi sono i requisiti minimi necessari per l'autenticazione del runbook con l'account RunAs.

Alcuni sottoscrittori creano un account di automazione usando un account utente di Azure AD per gestire la distribuzione classica di Azure o per le risorse di Azure Resource Manager.Some subscribers create an Automation account using an [Azure AD user account](automation-create-aduser-account.md) to manage Azure classic deployment or for Azure Resource Manager resources. Per mantenere la compatibilità con le versioni precedenti per questi `Add-AzureAccount` sottoscrittori, il meccanismo di autenticazione da utilizzare nel runbook è il cmdlet con un [asset delle credenziali.](automation-credentials.md) L'asset rappresenta un utente di Active Directory con accesso all'account Azure.The asset represents an Active Directory user with access to the Azure account.

È possibile abilitare questa funzionalità per il runbook grafico aggiungendo `Add-AzureAccount` un asset delle credenziali all'area di disegno, seguita da un'attività che usa l'asset delle credenziali per l'input. Vedere l'esempio seguente.

![Attività di autenticazione](media/automation-graphical-authoring-intro/authentication-activities.png)

Il runbook deve eseguire l'autenticazione all'avvio e dopo ogni checkpoint. Pertanto è `Add-AzureAccount` necessario utilizzare `Checkpoint-Workflow` un'attività dopo qualsiasi attività. Non è necessario utilizzare un'attività di credenziali aggiuntiva.

![Activity Output](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Esportazione e importazione di un runbook con interfaccia grafica

È possibile esportare solo la versione pubblicata di un runbook con interfaccia grafica. Se il runbook non è ancora stato pubblicato, il pulsante **Esporta** è disabilitato. Quando si fa clic sul pulsante **Esporta,** il runbook viene scaricato nel computer locale. Il nome del file corrisponde al nome del runbook con estensione **.graphrunbook.**

È possibile importare un file di runbook di PowerShell Workflow grafico o grafico selezionando l'opzione **Importa** quando si aggiunge un runbook. Quando si seleziona il file da importare, è possibile mantenere lo stesso nome o specificarne uno nuovo. Il campo **Tipo di runbook** visualizza il tipo di runbook dopo aver valutato il file selezionato. Se si tenta di selezionare un tipo diverso che non è corretto, l'editor grafico presenta un messaggio che indica che sono presenti potenziali conflitti e che potrebbero verificarsi errori di sintassi durante la conversione.

![Importare runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Test di un Runbook grafico

Ogni runbook grafico in Automazione di Azure ha una versione Bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza è pronta per l'uso, la si pubblica, sovrascrivendo la versione pubblicata corrente con la versione bozza.

È possibile testare la versione bozza di un runbook nel portale di Azure lasciando invariata la versione pubblicata. In alternativa, è possibile testare un nuovo runbook prima che sia stato pubblicato in modo da poter verificare che il runbook funzioni correttamente prima della sostituzione di qualsiasi versione. Il test di un runbook esegue la versione Bozza e garantisce che tutte le azioni eseguite vengano completate. Non viene creata alcuna cronologia dei processi, ma nel riquadro Output test viene visualizzato l'output.

Aprire il controllo Test per il runbook grafico aprendo il runbook per la modifica e quindi facendo clic su **Riquadro di test**. Il controllo Test richiede parametri di input ed è possibile avviare il runbook facendo clic su **Start**.

## <a name="publishing-a-graphical-runbook"></a>Pubblicazione di un Runbook grafico

Pubblicare un runbook grafico aprendo il runbook per la modifica e facendo clic su **Pubblica**. Gli stati possibili per il runbook sono:

* Nuovo: il runbook non è ancora stato pubblicato. 
* Pubblicato -- il runbook è stato pubblicato.
* In modifica: il runbook è stato modificato dopo la pubblicazione e le versioni Draft e Published sono diverse.

![Stati del Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

È possibile ripristinare la versione pubblicata di un runbook. Questa operazione consente di eliminare tutte le modifiche apportate dall'ultima pubblicazione del runbook. Sostituisce la versione Bozza del runbook con la versione pubblicata.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per iniziare a utilizzare i runbook grafici, vedere [Il mio primo runbook grafico](automation-first-runbook-graphical.md).
* Per altre informazioni sui tipi di runbook e sui relativi vantaggi e limitazioni, vedere [Tipi di runbook di Automazione di Azure.](automation-runbook-types.md)
* Per informazioni su come eseguire l'autenticazione usando l'account RunAs di [automazione, vedere Configurare l'account RunAs](automation-sec-configure-azure-runas-account.md)di Azure.
