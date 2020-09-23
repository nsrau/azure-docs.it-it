---
title: Creare runbook grafici in Automazione di Azure
description: Questo articolo descrive come creare un runbook grafico senza usare il codice.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 161272fe35ee9ea1e0880b991273e5d1a79eafb4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987330"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Creare runbook grafici in Automazione di Azure

Tutti i runbook in Automazione di Azure sono flussi di lavoro di Windows PowerShell. I runbook grafici e i runbook grafici del flusso di lavoro di PowerShell generano un codice di PowerShell che viene eseguito da chi usa Automazione, ma che non può essere visualizzato o modificato dagli utenti. È possibile convertire un runbook grafico in un runbook grafico del flusso di lavoro di PowerShell e viceversa. Tuttavia, non è possibile convertire questi runbook in un runbook di testo. L'editor grafico di Automazione non può importare un runbook testuale.

La creazione grafica consente di creare runbook per Automazione di Azure senza le complessità del codice di Windows PowerShell o del flusso di lavoro PowerShell sottostante. È possibile aggiungere attività da una libreria di cmdlet e runbook in un canvas e collegarle per configurarle in modo da formare un flusso di lavoro. Se si è già lavorato con System Center Orchestrator o Service Management Automation, si dovrebbe avere familiarità con la creazione grafica. Questo articolo offre un'introduzione ai concetti necessari per iniziare la creazione di un runbook grafico.

## <a name="overview-of-graphical-editor"></a>Panoramica dell'editor grafico

È possibile aprire l'editor grafico nel portale di Azure creando o modificando un Runbook grafico.

![Area di lavoro grafica](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Le sezioni seguenti descrivono i controlli dell'editor grafico.

### <a name="canvas-control"></a>Controllo Canvas

Il controllo Canvas consente di progettare i runbook. È possibile aggiungere attività al runbook dai nodi del controllo Libreria e connetterle con collegamenti per definire la logica del runbook. Nella parte inferiore del canvas sono presenti i controlli che consentono di fare zoom avanti e indietro.

### <a name="library-control"></a>Controllo Library

Il controllo Libreria consente di selezionare le [attività](#use-activities) da aggiungere al runbook. Dopo averle aggiunte al canvas è possibile collegarle ad altre attività. Il controllo Libreria include le sezioni definite nella tabella seguente.

| Sezione | Descrizione |
|:--- |:--- |
| Cmdlet |Tutti i cmdlet che possono essere usati nel runbook. I cmdlet sono organizzati per modulo. Saranno disponibili tutti i moduli installati nell'account di Automazione. |
| Runbook |I runbook dell'account di Automazione. Questi runbook possono essere aggiunti al canvas per essere usati come runbook figlio. Vengono visualizzati solo i runbook dello stesso tipo dei runbook da modificare. Per i runbook grafici vengono visualizzati solo i runbook basati su PowerShell. Per i runbook grafici del flusso di lavoro di PowerShell, vengono visualizzati solo i runbook basati sul flusso di lavoro di PowerShell. |
| Asset |Gli [asset di automazione](/previous-versions/azure/dn939988(v=azure.100)) dell'account di Automazione che è possibile usare nel runbook. Quando si aggiunge un asset a un runbook, viene aggiunta un'attività del flusso di lavoro che ottiene l'asset selezionato. Nel caso di attività di variabile, è possibile selezionare se aggiungere un'attività per ottenere la variabile o per impostarla. |
| Runbook Control |Attività di controllo che è possibile usare nel runbook corrente. Un'attività Giunzione accetta più input e attende che vengano completati tutti prima di continuare il flusso di lavoro. Un'attività Codice esegue una o più righe del codice di PowerShell o del flusso di lavoro di PowerShell a seconda del tipo di runbook grafico. È possibile usare questa attività per il codice personalizzato o per funzionalità difficili da ottenere con altre attività. |

### <a name="configuration-control"></a>Controllo Configuration

Il controllo Configurazione consente di specificare i dettagli per un oggetto selezionato nel canvas. Le proprietà disponibili in questo controllo dipendono dal tipo di oggetto selezionato. Quando si seleziona un'opzione nel controllo Configurazione, si aprono pannelli aggiuntivi per inserire le informazioni.

### <a name="test-control"></a>Controllo Test

Il controllo Test non viene visualizzato al primo avvio dell'editor grafico. Si apre quando si testa un runbook grafico in modo interattivo.

## <a name="use-activities"></a>Usare le attività

Le attività sono i blocchi predefiniti di un Runbook. Un'attività può essere un cmdlet di PowerShell, un runbook figlio o un flusso di lavoro. Per aggiungere un'attività al runbook, fare clic con il pulsante destro del mouse sul controllo Libreria e scegliere **Add to canvas** (Aggiungi al canvas). È quindi possibile fare clic e trascinare l'attività per posizionarla in un punto qualsiasi nel canvas desiderato. La posizione dell'attività sul canvas non influirà sul funzionamento del runbook. È possibile modificare il layout del runbook nel modo più adatto a visualizzarne il funzionamento.

![Add to canvas](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Selezionare un'attività nel canvas per configurarne le proprietà e i parametri nel pannello Configurazione. È possibile modificare l'etichetta dell'attività specificando un nome descrittivo per l'utente. Il runbook esegue ancora il cmdlet originale. In questo modo si modifica soltanto il nome visualizzato in uso nell'editor grafico. Si noti che l'etichetta deve essere univoca all'interno del runbook.

### <a name="parameter-sets"></a>Set di parametri

Un set di parametri definisce i parametri obbligatori e facoltativi che accetteranno i valori per un cmdlet specifico. Tutti i cmdlet dispongono di almeno un set di parametri, altri ne hanno più di uno. Se un cmdlet ha più set di parametri, sarà necessario selezionare quello da usare prima di configurare i parametri. È possibile modificare il set di parametri usato da un'attività selezionando **Set di parametri** e scegliendo un altro set. In questo caso, i valori dei parametri già configurati vengono persi.

Nell'esempio seguente il cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0&preserve-view=true) dispone di tre set di parametri. L'esempio usa un set denominato **ListVirtualMachineInResourceGroupParamSet**, con un solo parametro facoltativo, per restituire tutte le macchine virtuali in un gruppo di risorse. L'esempio usa anche il set di parametri **GetVirtualMachineInResourceGroupParamSet** per specificare la macchina virtuale da restituire. Questo set ha due parametri obbligatori e un parametro facoltativo.

![Parameter Set](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valori dei parametri

Quando si specifica un valore per un parametro, è necessario selezionare un'origine dati per determinare come verrà specificato il valore. Le origini dati disponibili per un determinato parametro dipendono dai valori validi per il parametro. Ad esempio, Null non sarà disponibile come opzione di un parametro che non consente valori Null.

| origine dati | Descrizione |
|:--- |:--- |
| Constant Value |Digitare un valore per il parametro. Questa origine dati è disponibile solo per i tipi di dati seguenti: Int32, Int64, Stringa, Booleano, Datetime, Commutazione. |
| Activity Output |Usare l'output di un'attività che precede l'attività corrente nel flusso di lavoro. Verranno elencate tutte le attività valide. Per il valore del parametro, usare solo l'attività che produce l'output. Se l'attività restituisce un oggetto con più proprietà, è possibile digitare il nome di una proprietà specifica dopo averla selezionata. |
| Input di runbook |Selezionare un input di runbook come un input per il parametro dell'attività. |
| Asset della variabile |Selezionare una variabile di Automazione come input. |
| Asset di credenziali |Selezionare una credenziale di Automazione come input. |
| Asset del certificato |Selezionare un certificato di Automazione come input. |
| Asset di connessione |Selezionare una connessione di Automazione come input. |
| PowerShell Expression |Specificare un'[espressione di PowerShell](#work-with-powershell-expressions) semplice. L'espressione verrà valutata prima dell'attività e il risultato verrà usato per il valore del parametro. È possibile usare variabili per fare riferimento all'output di un'attività o un parametro di input di Runbook. |
| Non configurato |Cancellare qualsiasi valore precedentemente configurato. |

#### <a name="optional-additional-parameters"></a>Parametri aggiuntivi facoltativi

Per tutti i cmdlet è possibile specificare parametri aggiuntivi. Si tratta di parametri comuni di PowerShell o di altri parametri personalizzati. L'editor grafico presenta una casella di testo in cui è possibile specificare i parametri usando la sintassi di PowerShell. Per usare ad esempio il parametro comune `Verbose`, specificare `-Verbose:$True`.

### <a name="retry-activity"></a>Ripetere l'attività

La funzionalità di ripetizione dei tentativi per un'attività consente di eseguire un'attività più volte finché non viene soddisfatta una determinata condizione, come un ciclo. È possibile usare questa funzionalità per le attività da eseguire più volte, soggette a errori e che potrebbero richiedere più di un tentativo per riuscire o per testare le informazioni di output dell'attività al fine di controllarne la validità dei dati.

Quando si abilita la ripetizione dei tentativi per un'attività, è possibile impostare un ritardo e una condizione. Il ritardo è il tempo, espresso in secondi o minuti, che il runbook attenderà prima di eseguire nuovamente l'attività. Se non si specifica un ritardo, l'attività viene eseguita nuovamente non appena viene completata.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Screenshot delle impostazioni della funzionalità Abilita ripetizione tentativi.":::

La condizione di ripetizione dei tentativi è un'espressione di PowerShell che viene valutata dopo ogni esecuzione dell'attività. Se l'espressione restituisce True, l'attività viene eseguita di nuovo. Se l'espressione restituisce False, l'attività non viene eseguita di nuovo e il runbook passa all'attività successiva.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Screenshot che mostra il nuovo tentativo fino a quando questa condizione è true Field ed esempi di espressioni di PowerShell che possono essere usate nella condizione di ripetizione dei tentativi.":::

La condizione di ripetizione dei tentativi può usare una variabile denominata `RetryData` che consente di accedere alle informazioni sulla ripetizione dei tentativi dell'attività. Questa variabile include le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| `NumberOfAttempts` |Numero di volte in cui l'attività è stata eseguita. |
| `Output` |Output dell'ultima esecuzione dell'attività. |
| `TotalDuration` |Tempo trascorso dal primo avvio dell'attività. |
| `StartedAt` |Ora in formato UTC del primo avvio dell'attività. |

Ecco alcuni esempi di condizioni per la ripetizione dei tentativi dell'attività.

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

Dopo aver configurato la condizione di ripetizione dei tentativi per un'attività, l'attività include due segnali visivi come promemoria per l'utente. Uno viene visualizzato all'interno dell'attività e l'altro durante il controllo della configurazione dell'attività.

![Segnali visivi di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controllo Workflow Script

Un controllo Script del flusso di lavoro è un'attività speciale che accetta lo script di PowerShell o del flusso di lavoro di PowerShell a seconda del tipo di runbook grafico che si sta creando. Questo controllo offre funzionalità che altrimenti potrebbero non essere disponibili. Non può accettare parametri, ma può usare le variabili per parametri di output di attività e di input di Runbook. Tutti gli output dell'attività vengono aggiunti al bus di dati. Un'eccezione viene restituita senza collegamenti in uscita, nel qual caso l'output viene aggiunto all'output del runbook.

Il codice seguente ad esempio esegue calcoli di data usando una variabile di input di runbook denominata `NumberOfDays`. Invia quindi un valore datetime calcolato come output che verrà usato dalle attività successive nel runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Usare i collegamenti per il flusso di lavoro

Un collegamento in un runbook grafico connette due attività. Viene visualizzato nel canvas come una freccia che punta dall'attività di origine verso l'attività di destinazione. Le attività vengono eseguite nella direzione della freccia, con l'attività di destinazione che inizia dopo il completamento dell'attività di origine.

### <a name="create-a-link"></a>Creare un collegamento

È possibile creare un collegamento tra due attività selezionando l'attività di origine e facendo clic sul cerchio nella parte inferiore del riquadro. Trascinare la freccia sull'attività di destinazione e rilasciare.

![Creare un collegamento](media/automation-graphical-authoring-intro/create-link-options.png)

Selezionare il collegamento per configurare le relative proprietà nel pannello Configuration, incluso il tipo di collegamento che viene descritto nella tabella seguente.

| Tipo di collegamento | Descrizione |
|:--- |:--- |
| Pipeline |L'attività di destinazione viene eseguita una volta per ogni oggetto restituito dall'attività di origine. L'attività di destinazione non viene eseguita se l'attività di origine non genera alcun output. L'output dell'attività di origine è disponibile come oggetto. |
| Sequenza |L'attività di destinazione viene eseguita solo una volta quando riceve l'output dall'attività di origine. L'output dell'attività di origine è disponibile come matrice di oggetti. |

### <a name="start-runbook-activity"></a>Avviare l'attività del runbook

Un runbook grafico inizia con le attività che non dispongono di un collegamento in ingresso. Spesso è presente una sola attività che svolge la funzione di attività iniziale per il runbook. Se più attività non dispongono di un collegamento in ingresso, il runbook si avvierà eseguendole in parallelo. Seguirà i collegamenti per eseguire altre attività man mano che vengono completate.

### <a name="specify-link-conditions"></a>Specificare le condizioni di collegamento

Se si specifica una condizione su un collegamento, l'attività di destinazione verrà eseguita solo se la condizione restituisce True. Verrà usata in genere una variabile `ActivityOutput` in una condizione per recuperare l'output dell'attività di origine.

Per un collegamento di tipo pipeline, è necessario specificare una condizione per un singolo oggetto. Il runbook valuta la condizione per ogni oggetto restituito dall'attività di origine. Esegue quindi l'attività di destinazione per ogni oggetto che soddisfa la condizione. Con un'attività di origine di `Get-AzVM` ad esempio è possibile usare la sintassi seguente in modo che un collegamento di tipo pipeline condizionale recuperi solo le macchine virtuali nel gruppo di risorse denominato Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Per un collegamento di tipo sequenza, il runbook valuta la condizione solo una volta, poiché viene restituita una singola matrice contenente tutti gli oggetti dell'attività di origine. Per questo motivo, il runbook non può usare un collegamento di sequenza per il filtraggio, come avviene con un collegamento di tipo pipeline. Il collegamento di tipo sequenza può determinare solo se l'attività successiva viene eseguita.

Si consideri ad esempio il set di attività seguente nel runbook **Avvia macchina virtuale**:

![Collegamento condizionale con sequenze](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Il runbook usa tre diversi collegamenti di sequenza che verificano i valori dei parametri di input `VMName` e `ResourceGroupName` per determinare l'azione appropriata da intraprendere. Le possibili azioni sono avviare una sola macchina virtuale, avviare tutte le macchine virtuali nel gruppo di risorse o avviare tutte le macchine virtuali in una sottoscrizione. Per il collegamento di tipo sequenza tra `Connect to Azure` e `Get single VM`, di seguito è illustrata la logica della condizione:

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

Quando si usa un collegamento condizionale, i dati disponibili dall'attività di origine per altre attività nel ramo vengono filtrati dalla condizione. Se un'attività è all'origine di più collegamenti, i dati disponibili per le attività in ogni ramo variano in base alla condizione nel collegamento connesso a tale ramo.

L'attività `Start-AzVM` nel runbook seguente ad esempio avvia tutte le macchine virtuali. Dispone di due collegamenti condizionali. Il primo collegamento condizionale usa l'espressione `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` per applicare un filtro che si basa sul corretto completamento dell'attività `Start-AzVM`. Il secondo collegamento condizionale usa l'espressione `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` per applicare un filtro che si basa sul mancato avvio della macchina virtuale da parte dell'attività `Start-AzVm`.

![Esempio di collegamento condizionale](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualsiasi attività che segue il primo collegamento e usa l'output dell'attività di `Get-AzureVM` recupera solo le macchine virtuali che sono state avviate al momento dell'esecuzione di `Get-AzureVM`. Un'attività che segue il secondo collegamento recupera solo le macchine virtuali che erano state arrestate al momento dell'esecuzione di `Get-AzureVM`. Le attività che seguono il terzo collegamento recuperano tutte le macchine virtuali, indipendentemente dallo stato di esecuzione.

### <a name="use-junctions"></a>Usare le giunzioni

Una giunzione è un'attività speciale che attende il completamento di tutti i rami in ingresso. Questo consente al runbook di eseguire più attività in parallelo e assicurarsi che siano state tutte completate prima di procedere.

Mentre una giunzione può disporre di un numero illimitato di collegamenti in entrata, solo uno di essi può essere una pipeline. Il numero di collegamenti di tipo sequenza in ingresso non prevede vincoli. È possibile creare la giunzione con più collegamenti di tipo pipeline in ingresso e salvare il runbook, ma l'esecuzione restituirà esito negativo.

L'esempio seguente fa parte di un Runbook che avvia un set di macchine virtuali e scarica contemporaneamente patch da applicare a tali macchine. Usa una giunzione per verificare che entrambi i processi vengano completati prima che il runbook continui.

![giunzione](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Usare i cicli

Un ciclo si forma quando un'attività di destinazione si collega alla propria attività di origine o a un'altra attività che alla fine si collega all'origine. La creazione grafica attualmente non supporta i cicli. Se un runbook dispone di un ciclo, verrà salvato correttamente, ma riceverà un errore durante l'esecuzione.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Condividere i dati tra le attività

Tutti i dati restituiti da un'attività con un collegamento in uscita vengono scritti nel bus di dati del runbook. Le attività nel Runbook possono usare i dati del bus di dati per popolare valori di parametri o includere codice di script. Un'attività può accedere all'output di qualsiasi attività precedente nel flusso di lavoro.

I dati vengono scritti nel bus di dati secondo il tipo di collegamento dell'attività. Per un collegamento di tipo pipeline, i dati vengono restituiti come oggetti multipli. Per un collegamento di tipo sequenza, i dati vengono restituiti come matrice. Se è presente un solo valore, questo verrà restituito come matrice a elemento singolo.

Il runbook può accedere ai dati nel bus di dati con due modalità: 
* Usare un'origine dati dell'output dell'attività.
* Usare un'origine dati dell'espressione di PowerShell.

Il primo meccanismo usa un'origine dati di output dell'attività per popolare un parametro di un'altra attività. Se l'output è un oggetto, il runbook può specificare una singola proprietà.

![Output dell'attività](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Il secondo meccanismo di accesso ai dati recupera l'output di un'attività nell'origine dati di un'espressione di PowerShell o di un'attività di script del flusso di lavoro con una variabile `ActivityOutput`, usando la sintassi illustrata di seguito. Se l'output è un oggetto, il runbook può specificare una singola proprietà.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Usare i checkpoint

È possibile impostare [Checkpoint](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) in un runbook grafico del flusso di lavoro di PowerShell selezionando **Checkpoint per runbook** in una qualsiasi attività. In questo modo viene impostato un checkpoint dopo l'esecuzione dell'attività.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

I checkpoint sono abilitati solo nei runbook grafici del flusso di lavoro di PowerShell e non sono disponibili nei runbook grafici. Se il runbook usa i cmdlet di Azure, deve seguire qualsiasi attività sottoposta a checkpoint con un'attività `Connect-AzAccount`. L'operazione di connessione viene usata nel caso in cui il runbook viene sospeso e deve essere riavviato da questo checkpoint in un ruolo di lavoro diverso.

## <a name="handle-runbook-input"></a>Gestire gli input del runbook

Un runbook può richiedere l'input di un utente che avvia il runbook tramite il portale di Azure o di un altro runbook, se quello corrente viene usato come runbook figlio. Ad esempio per un runbook che crea una macchina virtuale, potrebbe essere necessario specificare informazioni come il nome della macchina virtuale e altre proprietà ogni volta che si avvia il runbook.

Il runbook accetta l'input definendo uno o più parametri di input. L'utente specifica i valori per questi parametri ogni volta che viene avviato il runbook. Quando l'utente avvia il runbook usando il portale di Azure, gli viene richiesto di specificare i valori per ogni parametro di input supportato dal runbook.

Quando si crea il runbook, è possibile accedere ai parametri di input facendo clic su **Input e output** sulla barra degli strumenti del runbook. Verrà aperto il controllo Input e output, in cui è possibile modificare un parametro di input esistente o crearne uno nuovo facendo clic su **Aggiungi input**.

![Add input](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Ogni parametro di input è definito dalle proprietà descritte nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| Nome | Obbligatorio. Nome del parametro. Il nome deve essere univoco nel runbook. Deve iniziare con una lettera e può contenere solo lettere, numeri e caratteri di sottolineatura. Il nome non può contenere spazi. |
| Descrizione |Facoltativa. Descrizione dello scopo del parametro di input. |
| Type | Facoltativa. Tipo di dati previsto per il valore del parametro. Il portale di Azure visualizzerà un controllo appropriato per il tipo di dati per ogni parametro quando viene richiesto l'input. I tipi di parametro supportati sono Stringa, Int32, Int64, Decimale, Booleano, Datetime e Oggetto. Se non è selezionato un tipo di dati, l'impostazione predefinita è Stringa.|
| Obbligatorio | Facoltativa. Impostazione che specifica se è necessario indicare un valore per il parametro. Se si sceglie `yes`, è necessario specificare un valore quando viene avviato il runbook. Se si sceglie `no`, non è necessario specificare un valore all'avvio del runbook ed è possibile usare un valore predefinito. Non è possibile avviare il runbook se non si specifica un valore per ogni parametro obbligatorio che non dispone di un valore predefinito. |
| Default Value | Facoltativa. Valore usato per un parametro se non ne viene passato uno all'avvio del runbook. Per impostare un valore predefinito, scegliere `Custom`. Selezionare `None` se non si vuole specificare alcun valore predefinito. |

## <a name="handle-runbook-output"></a>Gestire l'output del runbook

La creazione grafica salva i dati creati da un'attività che non dispone di un collegamento in uscita nell'[output del runbook](./automation-runbook-output-and-messages.md). L'output viene salvato con il processo di Runbook ed è disponibile per un Runbook padre quando il Runbook viene usato come Runbook figlio.

## <a name="work-with-powershell-expressions"></a>Usare le espressioni di PowerShell

Uno dei vantaggi della creazione grafica è la possibilità di creare un runbook con una conoscenza minima di PowerShell. Attualmente, però, è necessario avere una discreta conoscenza di PowerShell per popolare determinati [valori di parametri](#use-activities) e per impostare le [condizioni di collegamento](#use-links-for-workflow). Questa sezione offre una rapida introduzione alle espressioni di PowerShell. I dettagli completi di PowerShell sono disponibili all'indirizzo [Scripting con Windows PowerShell](/powershell/scripting/overview).

### <a name="use-a-powershell-expression-as-a-data-source"></a>Usare un'espressione di PowerShell come origine dati

È possibile usare un'espressione di PowerShell come origine dati per popolare il valore di un [parametro dell'attività](#use-activities) con i risultati del codice PowerShell. L'espressione potrebbe essere una singola riga di codice che esegue una funzione semplice o più righe che eseguono una logica complessa. L'output di un comando che non è assegnato a una variabile è l'output per il valore del parametro.

Il comando seguente, ad esempio, restituisce la data corrente.

```powershell-interactive
Get-Date
```

Il frammento di codice successivo compila una stringa della data corrente e l'assegna a una variabile. Il codice invia i contenuti della variabile all'output.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

I comandi seguenti valutano la data corrente e restituiscono una stringa che indica se il giorno corrente rappresenta un fine settimana o un giorno feriale.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Usare l'output dell'attività

Per usare l'output di una precedente attività nel runbook, usare la variabile `ActivityOutput` con la sintassi seguente.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Ad esempio, è possibile avere un'attività con una proprietà che richiede il nome di una macchina virtuale. In questo caso, il runbook può usare l'espressione seguente.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se la proprietà richiede l'oggetto macchina virtuale anziché un nome, il runbook restituisce l'intero oggetto usando la sintassi seguente.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Il runbook può usare l'output di un'attività in un'espressione più complessa, ad esempio la seguente. Questa espressione concatena il testo al nome della macchina virtuale.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Confrontare i valori

Utilizzare [operatori di confronto](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) per confrontare i valori o determinare se un valore corrisponde a un modello specificato. Un confronto restituisce un valore True o False.

Ad esempio, la condizione seguente determina se la macchina virtuale di un'attività denominata `Get-AzureVM` è attualmente è arrestata.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

La condizione seguente determina se la stessa macchina virtuale è in uno stato diverso da arrestata.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

È possibile aggiungere più condizioni nel runbook usando un [operatore logico](/powershell/module/microsoft.powershell.core/about/about_logical_operators), ad esempio `-and` o `-or`. Ad esempio, la condizione seguente verifica se la stessa macchina virtuale dell'esempio precedente presenta lo stato Arrestata o Arresto in corso.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>Usare le tabelle hash

Le [tabelle hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) sono coppie nome-valore utili per la restituzione di un set di valori. È anche possibile visualizzare una tabella hash indicata come dizionario. Le proprietà per determinate attività prevedono una tabella hash anziché un valore semplice.

Creare una tabella hash usando la sintassi seguente, che può contenere un numero qualsiasi di voci, ma ciascuna è definita da un nome e un valore.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Ad esempio, l'espressione seguente crea una tabella hash da usare come origine dati per un parametro di attività che prevede una tabella hash di valori per una ricerca in Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

L'esempio seguente usa l'output di un'attività denominata `Get Twitter Connection` per popolare una tabella hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Eseguire l'autenticazione nelle risorse di Azure

La maggior parte dei runbook di Automazione di Azure che gestisce risorse di Azure richiede l'autenticazione ad Azure. L'[account RunAs](./manage-runas-account.md), anche detto entità servizio, è il meccanismo predefinito usato dal runbook di Automazione per accedere alle risorse di Azure Resource Manager nella sottoscrizione. È possibile aggiungere questa funzionalità a un runbook grafico aggiungendo l'asset di connessione `AzureRunAsConnection`, che usa il cmdlet [Get-AutomationConnection](/system-center/sma/manage-global-assets) di PowerShell nel canvas. È anche possibile aggiungere il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Questo scenario è illustrato nell'esempio seguente.

![Attività di autenticazione RunAs](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

L'attività `Get Run As Connection` o `Get-AutomationConnection` è configurata con un'origine dati di valore costante denominata `AzureRunAsConnection`.

![Configurazione della connessione RunAs](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

L'attività successiva, `Connect-AzAccount`, aggiunge l'account RunAs autenticato per l'uso nel runbook.

![Set di parametri Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Si noti che questi alias non sono disponibili per i runbook grafici. Un runbook grafico può usare solo `Connect-AzAccount`.

Per i campi del parametro **APPLICATIONID**, **CERTIFICATETHUMBPRINT** e **TENANTID** specificare il nome della proprietà per il percorso del campo, perché l'attività restituisce un oggetto con più proprietà. Altrimenti, quando si esegue il runbook, il tentativo di autenticazione ha esito negativo. Questi sono i requisiti minimi necessari per l'autenticazione del runbook con l'account RunAs.

Alcuni sottoscrittori creano un account di Automazione usando un [account utente di Azure AD](./shared-resources/credentials.md) per gestire la distribuzione classica di Azure o per le risorse di Azure Resource Manager. Per mantenere la compatibilità con le versioni precedenti di questi sottoscrittori, il meccanismo di autenticazione da usare nel runbook è il cmdlet `Add-AzureAccount` con un [asset di credenziali](./shared-resources/credentials.md). L'asset rappresenta un utente di Active Directory con accesso all'account Azure.

È possibile abilitare questa funzionalità per il runbook grafico aggiungendo un asset di credenziali al canvas, seguito da un'attività `Add-AzureAccount` che usa l'asset di credenziali per l'input. Vedere l'esempio seguente.

![Attività di autenticazione](media/automation-graphical-authoring-intro/authentication-activities.png)

Il runbook deve eseguire l'autenticazione all'avvio e dopo ogni checkpoint. Pertanto, è necessario usare un'attività `Add-AzureAccount` dopo un'attività `Checkpoint-Workflow`. Non è necessario usare un'attività delle credenziali aggiuntiva.

![Activity Output](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Esportare un runbook grafico

È possibile esportare solo la versione pubblicata di un runbook con interfaccia grafica. Se il runbook non è ancora stato pubblicato, il pulsante **Esporta** è disabilitato. Quando si fa clic sul pulsante **Esporta**, il runbook viene scaricato nel computer locale. Il nome del file corrisponde al nome del runbook con un estensione **.graphrunbook**.

## <a name="import-a-graphical-runbook"></a>Importare un runbook grafico

È possibile importare un file di runbook grafico o di runbook grafico del flusso di lavoro di PowerShell selezionando l'opzione **Importa** quando si aggiunge un runbook. Quando si seleziona il file da importare, è possibile mantenere lo stesso nome oppure indicarne uno nuovo. Il campo **Tipo di runbook** mostra il tipo di runbook dopo aver valutato il file selezionato. Se si tenta di selezionare un tipo diverso che non è corretto, l'editor grafico presenta un messaggio che comunica che ci sono potenziali conflitti e che potrebbero essersi verificati errori di sintassi durante la conversione.

![Importare runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testare un runbook grafico

Ogni runbook grafico in Automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre la versione bozza può essere solo modificata. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza è pronta per l'uso, è possibile pubblicarla sovrascrivendo così la versione pubblicata con la versione bozza.

È possibile testare la versione bozza di un runbook nel portale di Azure lasciando invariata la versione pubblicata. In alternativa, è possibile testare un nuovo runbook prima di pubblicarlo, in modo da poter verificare che il runbook funzioni correttamente prima di qualsiasi sostituzione della versione. Il test del runbook esegue la versione bozza e garantisce che le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma l'output viene visualizzato nel riquadro Output del test.

Per aprire il controllo Test per il runbook grafico, aprire il runbook per la modifica e quindi fare clic sul **riquadro di test**. Il controllo Test richiederà i parametri di input e sarà possibile avviare il runbook facendo clic sul pulsante **Avvio**.

## <a name="publish-a-graphical-runbook"></a>Pubblicare un runbook grafico

Pubblicare un runbook grafico aprendolo per la modifica e quindi facendo clic su **Pubblica**. Gli stati possibili per il runbook sono:

* Nuovo: il runbook non è ancora stato pubblicato. 
* Pubblicato: il runbook è stato pubblicato.
* In modifica: il runbook è stato modificato dopo la pubblicazione e le versioni bozza e pubblicata sono diverse.

![Stati del Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

È possibile ripristinare la versione pubblicata di un runbook. Questa operazione elimina tutte le modifiche apportate dall'ultima pubblicazione del runbook. Sostituisce la versione bozza del runbook con la versione pubblicata.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook grafici, vedere [Esercitazione: creare un runbook grafico](learn/automation-tutorial-runbook-graphical.md).
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md).
* Per informazioni su come eseguire l'autenticazione usando l'account RunAs di Automazione, vedere [Account RunAs](automation-security-overview.md#run-as-account).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation).
