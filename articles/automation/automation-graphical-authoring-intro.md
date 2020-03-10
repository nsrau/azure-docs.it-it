---
title: Creazione grafica in Automazione di Azure
description: La creazione grafica consente di creare Runbook per Automazione di Azure senza usare codice. Questo articolo offre un'introduzione alla creazione grafica e descrive tutti i dettagli necessari per iniziare la creazione di un Runbook grafico.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a50dbe4d1e100032282891ccd15a94330f7fead4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373369"
---
# <a name="graphical-authoring-in-azure-automation"></a>Creazione grafica in Automazione di Azure

La creazione grafica consente di creare manuali operativi per automazione di Azure senza le complessità del codice del flusso di lavoro di Windows PowerShell o PowerShell sottostante. È possibile aggiungere attività all'area di disegno da una libreria di cmdlet e manuali operativi, collegarli insieme e configurarli per formare un flusso di lavoro. Se si è mai lavorato con l'agente di orchestrazione di System Center o con Service Management Automation (SMA), la creazione grafica dovrebbe avere un aspetto familiare. Questo articolo fornisce un'introduzione ai concetti necessari per iniziare a creare un Runbook grafico.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Runbook grafici

Tutti i manuali operativi in automazione di Azure sono flussi di lavoro di Windows PowerShell. Manuali operativi grafici e flussi di lavoro manuali operativi grafici di PowerShell generano codice PowerShell che i ruoli di lavoro di automazione eseguono ma che non è possibile visualizzare o modificare. È possibile convertire un Runbook grafico in un Runbook grafico del flusso di lavoro PowerShell e viceversa. Tuttavia, non è possibile convertire questi manuali operativi in un Runbook testuale. Inoltre, l'editor grafico di automazione non può importare un Runbook testuale.

## <a name="overview-of-graphical-editor"></a>Panoramica dell'editor grafico

È possibile aprire l'editor grafico nel portale di Azure creando o modificando un Runbook grafico.

![Area di lavoro grafica](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Le sezioni seguenti descrivono i controlli dell'editor grafico.

### <a name="canvas-control"></a>Canvas (controllo)

Il controllo Canvas consente di progettare i Runbook. È possibile aggiungere le attività dai nodi nel controllo libreria a Runbook e connetterle con i collegamenti per definire la logica di Runbook. Nella parte inferiore dell'area di disegno sono presenti controlli che consentono di eseguire lo zoom avanti e indietro.

### <a name="library-control"></a>Controllo Library

Il controllo libreria consente di selezionare le [attività](#activities) da aggiungere al Runbook. Aggiungerli all'area di disegno, in cui è possibile connetterli ad altre attività. Il controllo libreria include le sezioni definite nella tabella seguente.

| Sezione | Descrizione |
|:--- |:--- |
| Cmdlet |Tutti i cmdlet che possono essere usati in Runbook. I cmdlet sono organizzati per modulo. Sono disponibili tutti i moduli installati nell'account di automazione. |
| Runbook |Manuali operativi nell'account di automazione. È possibile aggiungere questi manuali operativi all'area di disegno da usare come manuali operativi figlio. Vengono visualizzati solo manuali operativi dello stesso tipo di base del Runbook da modificare. Per manuali operativi grafici vengono visualizzati solo manuali operativi basati su PowerShell. Per manuali operativi del flusso di lavoro PowerShell grafico, vengono visualizzati solo manuali operativi basati sul flusso di lavoro PowerShell. |
| Asset |[Asset di automazione](/previous-versions/azure/dn939988(v=azure.100)) nell'account di automazione che è possibile usare nella Runbook. L'aggiunta di un asset a un Runbook aggiunge un'attività del flusso di lavoro che ottiene l'asset selezionato. Nel caso di attività di variabile, è possibile selezionare se aggiungere un'attività per ottenere la variabile o per impostarla. |
| Runbook Control |Controllare le attività che possono essere usate nella Runbook corrente. Un'attività di giunzione accetta più input e attende il completamento di tutti prima di continuare il flusso di lavoro. Un'attività di codice esegue una o più righe di codice del flusso di lavoro PowerShell o PowerShell, a seconda del tipo di Runbook grafico. È possibile usare questa attività per il codice personalizzato o per funzionalità difficili da ottenere con altre attività. |

### <a name="configuration-control"></a>Controllo Configuration

Il controllo della configurazione consente di specificare i dettagli per un oggetto selezionato nell'area di disegno. Le proprietà disponibili in questo controllo dipendono dal tipo di oggetto selezionato. Quando si sceglie un'opzione nel controllo della configurazione, vengono aperti ulteriori pannelli per fornire ulteriori informazioni.

### <a name="test-control"></a>Controllo Test

Il controllo Test non viene visualizzato al primo avvio dell'editor grafico. Viene aperto quando si testa un Runbook grafico in modo interattivo.

## <a name="activities"></a>Attività

Le attività sono i blocchi predefiniti di un Runbook. Un'attività può essere un cmdlet di PowerShell, un Runbook figlio o un flusso di lavoro. È possibile aggiungere un'attività a Runbook facendo clic con il pulsante destro del mouse sul controllo libreria e selezionando **Aggiungi ad area di disegno**. È quindi possibile fare clic e trascinare l'attività per posizionarla in un punto qualsiasi nel canvas desiderato. Il percorso dell'attività nell'area di disegno non influisce sul funzionamento di Runbook. È possibile disporre il Runbook in qualsiasi modo sia più adatto per visualizzarne il funzionamento.

![Add to canvas](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selezionare un'attività nell'area di disegno per configurarne le proprietà e i parametri nel pannello configurazione. È possibile modificare l'etichetta dell'attività in un nome che si trova descrittivo. Il Runbook esegue ancora il cmdlet originale. Si sta semplicemente modificando il nome visualizzato usato dall'editor grafico. Si noti che l'etichetta deve essere univoca all'interno di Runbook.

### <a name="parameter-sets"></a>Set di parametri

Un set di parametri definisce i parametri obbligatori e facoltativi che accetteranno i valori per un cmdlet specifico. Per tutti i cmdlet è presente almeno un set di parametri e altri set. Se un cmdlet dispone di più set di parametri, è necessario selezionare quello da usare prima di poter configurare i parametri. È possibile modificare il set di parametri utilizzato da un'attività selezionando **set di parametri** e scegliendo un altro set. In questo caso, i valori dei parametri già configurati andranno persi.

Nell'esempio seguente il cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) include tre set di parametri. Nell'esempio viene usato un set denominato **ListVirtualMachineInResourceGroupParamSet**, con un unico parametro facoltativo, per la restituzione di tutte le macchine virtuali in un gruppo di risorse. Nell'esempio viene inoltre utilizzato il set di parametri **GetVirtualMachineInResourceGroupParamSet** per specificare la macchina virtuale da restituire. Questo set ha due parametri obbligatori e un parametro facoltativo.

![Parameter Set](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Valori parametro

Quando si specifica un valore per un parametro, è necessario selezionare un'origine dati per determinare come verrà specificato il valore. Le origini dati disponibili per un determinato parametro dipendono dai valori validi per il parametro. Ad esempio, **null** non è un'opzione disponibile per un parametro che non consente valori null.

| Origine dati | Descrizione |
|:--- |:--- |
| Valore costante |Digitare un valore per il parametro. Questa origine dati è disponibile solo per i tipi di dati seguenti: Int32, Int64, String, Boolean, DateTime, switch. |
| Activity Output |Utilizzare l'output di un'attività che precede l'attività corrente nel flusso di lavoro. Verranno elencate tutte le attività valide. Per il valore del parametro, usare solo l'attività che produce l'output. Se l'attività restituisce un oggetto con più proprietà, è possibile digitare il nome di una proprietà specifica dopo aver selezionato l'attività. |
| Input di runbook |Selezionare un input Runbook come input per il parametro Activity. |
| Asset della variabile |Selezionare una variabile di automazione come input. |
| Asset di credenziali |Selezionare una credenziale di automazione come input. |
| Asset del certificato |Selezionare un certificato di automazione come input. |
| Asset di connessione |Selezionare una connessione di automazione come input. |
| PowerShell Expression |Specificare una semplice [espressione PowerShell](#powershell-expressions). L'espressione viene valutata prima dell'attività e il risultato viene usato per il valore del parametro. È possibile usare variabili per fare riferimento all'output di un'attività o un parametro di input di Runbook. |
| Non configurato |Cancellare tutti i valori configurati in precedenza. |

#### <a name="optional-additional-parameters"></a>Parametri aggiuntivi facoltativi

Per tutti i cmdlet è possibile specificare parametri aggiuntivi. Si tratta di parametri comuni di PowerShell o di altri parametri personalizzati. Nell'editor grafico viene visualizzata una casella di testo in cui è possibile specificare parametri usando la sintassi di PowerShell. Ad esempio, per usare il parametro comune *verbose* , è necessario specificare `-Verbose:$True`.

### <a name="retry-activity"></a>Ripetere l'attività

La funzionalità di ripetizione dei tentativi per un'attività ne consente l'esecuzione più volte fino a quando non viene soddisfatta una determinata condizione, in modo analogo a un ciclo. È possibile utilizzare questa funzionalità per le attività che devono essere eseguite più volte, sono soggette a errori, potrebbe richiedere più di un tentativo di esito positivo o testare le informazioni di output dell'attività per i dati validi.

Quando si abilita la ripetizione dei tentativi per un'attività, è possibile impostare un ritardo e una condizione. Il ritardo è il tempo, espresso in secondi o minuti, che il runbook attenderà prima di eseguire nuovamente l'attività. Se non si specifica un ritardo, l'attività viene eseguita nuovamente immediatamente dopo il completamento.

![Ritardo di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/retry-delay.png)

La condizione di ripetizione dei tentativi è un'espressione di PowerShell che viene valutata dopo ogni esecuzione dell'attività. Se l'espressione viene risolta in true, l'attività viene eseguita di nuovo. Se l'espressione viene risolta in false, l'attività non viene eseguita di nuovo e il Runbook passa all'attività successiva.

![Ritardo di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/retry-condition.png)

La condizione di ripetizione dei tentativi può utilizzare una variabile denominata *RetryData* che consente di accedere alle informazioni sui tentativi di attività. Questa variabile include le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| NumberOfAttempts |Numero di volte in cui l'attività è stata eseguita. |
| Output |Output dell'ultima esecuzione dell'attività. |
| TotalDuration |Tempo trascorso dal primo avvio dell'attività. |
| StartedAt |Ora (in formato UTC) in cui l'attività è stata avviata per la prima volta. |

Di seguito sono riportati alcuni esempi di condizioni per i tentativi di attività.

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

Dopo aver configurato la condizione di ripetizione dei tentativi per un'attività, l'attività include due segnali visivi come promemoria per l'utente. Una viene presentata nell'attività e l'altra viene visualizzata quando si esamina la configurazione dell'attività.

![Segnali visivi di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Controllo Workflow Script

Un controllo di script del flusso di lavoro è un'attività speciale che accetta script di PowerShell o del flusso di lavoro PowerShell, a seconda del tipo di Runbook grafico creato. Questo controllo fornisce funzionalità che potrebbero non essere disponibili in altri modi. Non può accettare parametri, ma può usare le variabili per parametri di output di attività e di input di Runbook. Tutti gli output dell'attività vengono aggiunti a DataBus. Un'eccezione viene restituita senza collegamenti in uscita, nel qual caso l'output viene aggiunto all'output di Runbook.

Il codice seguente, ad esempio, esegue calcoli di data usando una variabile di input Runbook denominata *NumberOfDays*. Invia quindi un valore DateTime calcolato come output che verrà usato dalle attività successive in Runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Collegamenti e flusso di lavoro

Un collegamento in un Runbook grafico connette due attività. Viene visualizzato nel canvas come una freccia che punta dall'attività di origine verso l'attività di destinazione. Le attività vengono eseguite nella direzione della freccia, con l'attività di destinazione che inizia dopo il completamento dell'attività di origine.

### <a name="link-creation"></a>Creazione collegamento

È possibile creare un collegamento tra due attività selezionando l'attività di origine e facendo clic sul cerchio nella parte inferiore della forma. Trascinare la freccia sull'attività di destinazione e rilasciare.

![Creare un collegamento](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selezionare il collegamento per configurare le relative proprietà nel pannello Configuration, Le proprietà includono il tipo di collegamento, descritto nella tabella seguente.

| Tipo collegamento | Descrizione |
|:--- |:--- |
| Pipeline |L'attività di destinazione viene eseguita una volta per ogni oggetto restituito dall'attività di origine. L'attività di destinazione non viene eseguita se l'attività di origine non genera alcun output. L'output dell'attività di origine è disponibile come oggetto. |
| Sequenza |L'attività di destinazione viene eseguita una sola volta quando riceve l'output dall'attività di origine. L'output dell'attività di origine è disponibile come matrice di oggetti. |

### <a name="start-of-activity"></a>Inizio attività

Un runbook grafico inizia con le attività che non dispongono di un collegamento in ingresso. Spesso è presente una sola attività che funge da attività iniziale per Runbook. Se più attività non dispongono di un collegamento in ingresso, il Runbook viene avviato eseguendoli in parallelo. Seguirà i collegamenti per eseguire altre attività man mano che vengono completate.

### <a name="link-conditions"></a>Condizioni di collegamento

Quando si specifica una condizione per un collegamento, l'attività di destinazione viene eseguita solo se la condizione viene risolta in true. In genere si usa una variabile *ActivityOutput* in una condizione per recuperare l'output dall'attività di origine.

Per un collegamento di pipeline, è necessario specificare una condizione per un singolo oggetto. Runbook valuta la condizione per ogni oggetto restituito dall'attività di origine. Esegue quindi l'attività di destinazione per ogni oggetto che soddisfa la condizione. Ad esempio, con un'attività di origine di **Get-AzVM**, è possibile usare la sintassi seguente per un collegamento della pipeline condizionale per recuperare solo le macchine virtuali nel gruppo di risorse denominato group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Per un collegamento di sequenza, Runbook valuta solo la condizione una volta, poiché viene restituita una singola matrice contenente tutti gli oggetti dall'attività di origine. Per questo motivo, Runbook non può usare un collegamento di sequenza per il filtraggio, come può con un collegamento della pipeline. Il collegamento sequenza può semplicemente determinare se l'attività successiva viene eseguita o meno.

Ad esempio, eseguire il seguente set di attività nella Runbook di **avvio della macchina virtuale** :

![Collegamento condizionale con sequenze](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook usa tre diversi collegamenti di sequenza che verificano i valori dei parametri di input *VMName* e *ResourceGroupName* per determinare l'azione appropriata da eseguire. Le azioni possibili sono avviare una singola macchina virtuale, avviare tutte le macchine virtuali nel gruppo di risorse o avviare tutte le macchine virtuali in una sottoscrizione. Per il collegamento sequenza tra **Connetti ad Azure** e **ottenere una singola macchina virtuale**, di seguito è illustrata la logica della condizione:

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

Quando si usa un collegamento condizionale, i dati disponibili dall'attività di origine per altre attività nel ramo vengono filtrati dalla condizione. Se un'attività è l'origine di più collegamenti, i dati disponibili per le attività in ogni ramo dipendono dalla condizione nel collegamento che si connette al ramo.

Ad esempio, l'attività **Start-AzVM** nel runbook seguente avvia tutte le macchine virtuali. Dispone di due collegamenti condizionali. Il primo collegamento condizionale usa l'espressione `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` per filtrare se l'attività **Start-AzVM** viene completata correttamente. Il secondo collegamento condizionale usa l'espressione `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` per filtrare se l'attività **Start-AzVm** non riesce ad avviare la macchina virtuale.

![Esempio di collegamento condizionale](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualsiasi attività che segue il primo collegamento e usa l'output dell'attività di **Get-AzureVM** recupera solo le macchine virtuali avviate al momento dell'esecuzione di **Get-AzureVM** . Tutte le attività che seguono il secondo collegamento ottengono solo le macchine virtuali arrestate al momento dell'esecuzione di **Get-AzureVM** . Le attività che seguono il terzo collegamento recuperano tutte le macchine virtuali, indipendentemente dallo stato di esecuzione.

### <a name="junctions"></a>Giunzioni

Una giunzione è un'attività speciale che attende il completamento di tutti i rami in ingresso. Ciò consente all'Runbook di eseguire più attività in parallelo e assicurarsi che tutte siano state completate prima di procedere.

Mentre una giunzione può avere un numero illimitato di collegamenti in ingresso, solo uno di questi collegamenti può essere una pipeline. Il numero di collegamenti di tipo sequenza in ingresso non prevede vincoli. È possibile creare la giunzione con più collegamenti di pipeline in ingresso e salvare il Runbook, ma l'operazione avrà esito negativo quando viene eseguita.

L'esempio seguente fa parte di un Runbook che avvia un set di macchine virtuali e scarica contemporaneamente patch da applicare a tali macchine. Usa una giunzione per assicurarsi che entrambi i processi vengano completati prima che il Runbook continui.

![giunzione](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cicli

Un ciclo viene creato quando un'attività di destinazione si collega all'attività di origine o a un'altra attività che alla fine si collega all'origine. La creazione grafica non supporta attualmente i cicli. Se un runbook dispone di un ciclo, verrà salvato correttamente, ma riceverà un errore durante l'esecuzione.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Condivisione dei dati tra attività

Tutti i dati restituiti da un'attività con un collegamento in uscita vengono scritti in DataBus per Runbook. Le attività nel Runbook possono usare i dati del bus di dati per popolare valori di parametri o includere codice di script. Un'attività può accedere all'output di qualsiasi attività precedente nel flusso di lavoro.

I dati vengono scritti nel bus di dati secondo il tipo di collegamento dell'attività. Per un collegamento alla pipeline, i dati vengono restituiti come più oggetti. Per un collegamento di sequenza, i dati vengono restituiti come matrice. Se è presente un solo valore, viene restituito come matrice a elemento singolo.

Il Runbook ha due modi per accedere ai dati in DataBus: 
* Utilizzare un'origine dati di output dell'attività.
* Usare un'origine dati espressione PowerShell.

Il primo meccanismo usa un'origine dati di output dell'attività per popolare un parametro di un'altra attività. Se l'output è un oggetto, Runbook può specificare una singola proprietà.

![output attività](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Il secondo meccanismo di accesso ai dati recupera l'output di un'attività in un'origine dati di un'espressione di PowerShell o un'attività script del flusso di lavoro con una variabile *ActivityOutput* , usando la sintassi illustrata di seguito. Se l'output è un oggetto, il Runbook può specificare una singola proprietà.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Punti di controllo

È possibile impostare [Checkpoint](automation-powershell-workflow.md#checkpoints) in un Runbook del flusso di lavoro PowerShell grafico selezionando **Checkpoint Runbook** in qualsiasi attività. In questo modo viene impostato un checkpoint dopo l'esecuzione dell'attività.

![Punto di controllo](media/automation-graphical-authoring-intro/set-checkpoint.png)

I checkpoint sono abilitati solo nei manuali operativi grafici del flusso di lavoro PowerShell e non sono disponibili nei manuali operativi grafici. Se il Runbook usa i cmdlet di Azure, deve seguire qualsiasi attività con checkpoint con un'attività **Connect-AzAccount** . L'operazione Connect viene usata nel caso in cui il Runbook venga sospeso e deve essere riavviato da questo checkpoint in un ruolo di lavoro diverso.

## <a name="runbook-input-and-output"></a>Input e output di Runbook

### Input Runbook<a name="runbook-input"></a>

Un Runbook richiede l'input da un utente che avvia il Runbook tramite il portale di Azure o da un altro Runbook, se quello corrente viene usato come figlio. Ad esempio, per un Runbook che crea una macchina virtuale, l'utente potrebbe dover fornire tali informazioni come nome della macchina virtuale e altre proprietà ogni volta che il Runbook viene avviato.

Runbook accetta input definendo uno o più parametri di input. L'utente fornisce i valori per questi parametri ogni volta che il Runbook viene avviato. Quando l'utente avvia il Runbook usando il portale di Azure, all'utente viene richiesto di specificare i valori per ogni parametro di input supportato da Runbook.

Quando si crea la runbook, è possibile accedere ai parametri di input facendo clic sul pulsante **input e output** sulla barra degli strumenti di Runbook. Verrà visualizzato il controllo di input e output in cui è possibile modificare un parametro di input esistente o crearne uno nuovo facendo clic su **Aggiungi input**.

![Add input](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Ogni parametro di input è definito dalle proprietà descritte nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| Name | Obbligatoria. Nome del parametro. Il nome deve essere univoco all'interno di Runbook. Deve iniziare con una lettera e può contenere solo lettere, numeri e caratteri di sottolineatura. Il nome non può contenere uno spazio. |
| Descrizione |Facoltativa. Descrizione dello scopo del parametro di input. |
| Type | Facoltativa. Tipo di dati previsto per il valore del parametro. Il portale di Azure visualizzerà un controllo appropriato per il tipo di dati per ogni parametro quando viene richiesto l'input. I tipi di parametro supportati sono String, Int32, Int64, Decimal, Boolean, DateTime e Object. Se un tipo di dati non è selezionato, il valore predefinito è String.|
| Obbligatorio | Facoltativa. Impostazione che specifica se è necessario fornire un valore per il parametro. Se si sceglie **Sì**, è necessario fornire un valore all'avvio del Runbook. Se si sceglie **No**, non è necessario un valore quando viene avviato il Runbook ed è possibile utilizzare un valore predefinito. Non è possibile avviare runbook se non si specifica un valore per ogni parametro obbligatorio per il quale non è definito un valore predefinito. |
| Default Value | Facoltativa. Valore utilizzato per un parametro se non ne viene passato uno all'avvio del Runbook. Per impostare un valore predefinito, scegliere **Personalizzato**. Selezionare **nessuno** se non si vuole specificare alcun valore predefinito. |

### <a name="runbook-output"></a>Output del Runbook

La creazione grafica consente di salvare i dati creati da un'attività che non dispone di un collegamento in [uscita all'output di Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). L'output viene salvato con il processo di Runbook ed è disponibile per un Runbook padre quando il Runbook viene usato come Runbook figlio.

## <a name="powershell-expressions"></a>Espressioni PowerShell

Uno dei vantaggi della creazione grafica è che consente di creare un runbook con una conoscenza minima di PowerShell. Attualmente, tuttavia, è necessario avere a disposizione un po' di PowerShell per il popolamento di determinati [valori di parametro](#activities) e per l'impostazione delle [condizioni di collegamento](#links-and-workflow). In questa sezione viene fornita una rapida introduzione alle espressioni di PowerShell. I dettagli completi di PowerShell sono disponibili all'indirizzo [Scripting con Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Origine dati delle espressioni PowerShell

È possibile usare un'espressione di PowerShell come origine dati per popolare il valore di un [parametro Activity](#activities) con i risultati del codice PowerShell. L'espressione può essere costituita da una singola riga di codice che esegue una funzione semplice o più righe che eseguono una logica complessa. L'output di un comando che non è assegnato a una variabile è l'output per il valore del parametro.

Ad esempio, il comando seguente restituisce la data corrente.

```powershell-interactive
Get-Date
```

Il frammento di codice successivo compila una stringa dalla data corrente e la assegna a una variabile. Il codice invia il contenuto della variabile all'output.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

I comandi seguenti valutano la data corrente e restituiscono una stringa che indica se il giorno corrente è un fine settimana o un giorno feriale.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Activity Output

Per usare l'output di un'attività precedente nel Runbook, usare la variabile *ActivityOutput* con la sintassi seguente.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Ad esempio, è possibile avere un'attività con una proprietà che richiede il nome di una macchina virtuale. In questo caso, il Runbook può usare l'espressione seguente.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Se la proprietà richiede l'oggetto macchina virtuale anziché un solo nome, Runbook restituisce l'intero oggetto usando la sintassi seguente.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook può utilizzare l'output di un'attività in un'espressione più complessa, ad esempio la seguente. Questa espressione concatena il testo al nome della macchina virtuale.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Condizioni

Utilizzare [operatori di confronto](https://technet.microsoft.com/library/hh847759.aspx) per confrontare i valori o determinare se un valore corrisponde a un modello specificato. Un confronto restituisce un valore true o false.

Ad esempio, la condizione seguente determina se la macchina virtuale di un'attività denominata **Get-AzureVM** è attualmente arrestata.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

La condizione seguente determina se la stessa macchina virtuale si trova in uno stato diverso da arrestato.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

È possibile unire più condizioni in Runbook usando un [operatore logico](https://technet.microsoft.com/library/hh847789.aspx), ad esempio **-and** o **-or**. Ad esempio, la seguente condizione verifica se la macchina virtuale nell'esempio precedente è in uno stato interrotto **o** **arrestato** .

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Tabelle hash

Le [tabelle hash](https://technet.microsoft.com/library/hh847780.aspx) sono coppie nome-valore utili per la restituzione di un set di valori. È anche possibile che venga visualizzata una tabella hash denominata Dictionary. Le proprietà di determinate attività prevedono una tabella hash anziché un valore semplice.

Creare una tabella hash usando la sintassi seguente. Può contenere qualsiasi numero di voci, ma ognuna è definita da un nome e un valore.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Nell'espressione seguente, ad esempio, viene creata una tabella hash da utilizzare come origine dati per un parametro Activity che prevede una tabella hash di valori per una ricerca Internet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Nell'esempio seguente viene utilizzato l'output di un'attività denominata **Get Twitter Connection** per popolare una tabella hash.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Autenticazione per le risorse di Azure

La maggior parte dei runbook di Automazione di Azure che gestisce risorse di Azure richiede l'autenticazione ad Azure. L' [account RunAs](automation-create-runas-account.md), noto anche come entità servizio, è il meccanismo predefinito usato da un Runbook di automazione per accedere alle risorse Azure Resource Manager nella sottoscrizione. È possibile aggiungere questa funzionalità a un Runbook grafico tramite l'aggiunta dell'asset di connessione **AzureRunAsConnection** , che usa il cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) di PowerShell, all'area di disegno. È anche possibile aggiungere il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Questo scenario è illustrato nell'esempio seguente.

![Attività di autenticazione RunAs](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

L' **attività Get Run As Connection**, o **Get-AutomationConnection**, è configurata con un'origine dati di valore costante denominata **AzureRunAsConnection**.

![Configurazione della connessione RunAs](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

L'attività successiva, **Connect-AzAccount**, aggiunge l'account RunAs autenticato per l'uso in Runbook.

![Set di parametri Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Per PowerShell manuali operativi, **Add-AzAccount** e **Add-AzureRMAccount** sono alias per **Connect-AzAccount**. Si noti che questi alias non sono disponibili per il manuali operativi grafico. Un Runbook grafico può usare solo **Connect-AzAccount** .

Per i campi parametro, **APPLICATIONID**, **CERTIFICATETHUMBPRINT**e **TENANTID**, specificare il nome della proprietà per il percorso del campo, poiché l'attività restituisce un oggetto con più proprietà. In caso contrario, quando viene eseguito il Runbook, si verifica un errore durante il tentativo di autenticazione. Questi sono i requisiti minimi necessari per l'autenticazione del runbook con l'account RunAs.

Alcuni sottoscrittori creano un account di automazione usando un [account utente Azure ad](automation-create-aduser-account.md) per gestire la distribuzione classica di Azure o per Azure Resource Manager risorse. Per mantenere la compatibilità con le versioni precedenti per questi sottoscrittori, il meccanismo di autenticazione da usare in Runbook è il cmdlet **Add-AzureAccount** con un [asset delle credenziali](automation-credentials.md). L'asset rappresenta un utente Active Directory con accesso all'account Azure.

È possibile abilitare questa funzionalità per i Runbook grafici aggiungendo un asset delle credenziali all'area di disegno, seguito da un'attività **Add-AzureAccount** che usa l'asset delle credenziali per l'input. Vedere l'esempio seguente.

![Attività di autenticazione](media/automation-graphical-authoring-intro/authentication-activities.png)

Il Runbook deve eseguire l'autenticazione all'inizio e dopo ogni checkpoint. Pertanto, è necessario utilizzare un'attività **Add-AzureAccount** dopo qualsiasi attività **Checkpoint-Workflow** . Non è necessario utilizzare un'attività delle credenziali aggiuntiva.

![Activity Output](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Esportazione e importazione di un runbook con interfaccia grafica

È possibile esportare solo la versione pubblicata di un runbook con interfaccia grafica. Se il Runbook non è ancora stato pubblicato, il pulsante **Esporta** è disabilitato. Quando si fa clic sul pulsante **Esporta** , il Runbook viene scaricato nel computer locale. Il nome del file corrisponde al nome di runbook con l'estensione **. graphrunbook** .

È possibile importare un file Runbook grafico o grafico di un flusso di lavoro PowerShell selezionando l'opzione di **importazione** durante l'aggiunta di un Runbook. Quando si seleziona il file da importare, è possibile usare lo stesso nome o specificarne uno nuovo. Il campo **tipo di Runbook** Visualizza il tipo di runbook dopo aver valutato il file selezionato. Se si tenta di selezionare un tipo diverso che non è corretto, nell'editor grafico viene visualizzato un messaggio in cui si nota che ci sono potenziali conflitti e potrebbero essersi verificati errori di sintassi durante la conversione.

![Importare runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

## <a name="testing-a-graphical-runbook"></a>Test di un Runbook grafico

Ogni Runbook grafico in automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza è pronta per l'uso, la si pubblica, che sovrascrive la versione pubblicata corrente con la versione bozza.

È possibile testare la versione bozza di un Runbook nel portale di Azure lasciando invariata la versione pubblicata. In alternativa, è possibile testare un nuovo Runbook prima che sia stato pubblicato, in modo da poter verificare che il Runbook funzioni correttamente prima di qualsiasi sostituzione della versione. Il test di un Runbook esegue la versione bozza e garantisce che tutte le azioni eseguite siano completate. Non viene creata alcuna cronologia processo, ma nel riquadro di output del test viene visualizzato l'output.

Aprire il controllo di test per il Runbook grafico aprendo il Runbook per la modifica e quindi facendo clic su **riquadro di test**. Il controllo di test richiede parametri di input ed è possibile avviare il Runbook facendo clic su **Avvia**.

## <a name="publishing-a-graphical-runbook"></a>Pubblicazione di un Runbook grafico

Pubblicare un Runbook grafico aprendo il Runbook per la modifica e quindi facendo clic su **pubblica**. Gli stati possibili per Runbook sono i seguenti:

* Nuovo: il Runbook non è ancora stato pubblicato. 
* Pubblicato: il Runbook è stato pubblicato.
* In Edit (Runbook) è stato modificato dopo la pubblicazione e le versioni Draft e Published sono diverse.

![Stati del Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Si ha la possibilità di ripristinare la versione pubblicata di un Runbook. Questa operazione Elimina tutte le modifiche apportate dall'ultima pubblicazione di Runbook. Sostituisce la versione bozza di runbook con la versione pubblicata.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per iniziare a usare manuali operativi grafici, vedere [il primo Runbook grafico](automation-first-runbook-graphical.md).
* Per altre informazioni sui tipi di Runbook e i relativi vantaggi e limitazioni, vedere [tipi di Runbook di automazione di Azure](automation-runbook-types.md).
* Per informazioni su come eseguire l'autenticazione con l'account RunAs di automazione, vedere [configurare un account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
