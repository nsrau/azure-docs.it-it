---
title: Creazione grafica in Automazione di Azure | Microsoft Docs
description: La creazione grafica consente di creare Runbook per Automazione di Azure senza usare codice. Questo articolo offre un'introduzione alla creazione grafica e descrive tutti i dettagli necessari per iniziare la creazione di un Runbook grafico.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/03/2016
ms.author: magoedte;bwren

---
# Creazione grafica in Automazione di Azure
## Introduzione
La creazione grafica consente di creare runbook per Automazione di Azure senza le complessità del codice di Windows PowerShell o del flusso di lavoro PowerShell sottostante. È possibile aggiungere attività da una libreria di cmdlet e runbook in un canvas e collegarle per configurare un flusso di lavoro. Se si è già lavorato con System Center Orchestrator o Service Management Automation (SMA), si dovrebbe avere familiarità con queste operazioni.

Questo articolo offre un'introduzione alla creazione grafica e descrive i concetti necessari per iniziare la creazione di un Runbook grafico.

## Runbook grafici
Tutti i Runbook in Automazione di Azure sono flussi di lavoro di Windows PowerShell. I runbook grafici e i runbook grafici del flusso di lavoro PowerShell generano codice di PowerShell che viene eseguito dai computer di lavoro di Automazione, ma che non può essere visualizzato o modificato direttamente dagli utenti. Un runbook grafico può essere convertito in un runbook grafico del flusso di lavoro PowerShell e viceversa, ma entrambi non possono essere convertiti in un runbook testuale. Non è possibile importare un runbook testuale esistente nell'editor grafico.

## Panoramica dell'editor grafico
È possibile aprire l'editor grafico nel portale di Azure creando o modificando un Runbook grafico.

![Area di lavoro grafica](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Le sezioni seguenti descrivono i controlli dell'editor grafico.

### Canvas
Il canvas è l'area in cui vengono progettati i Runbook. È possibile aggiungere attività al Runbook dai nodi del controllo Library e connetterle con collegamenti per definire la logica del Runbook.

È possibile utilizzare i controlli nella parte inferiore dell'area di disegno per fare zoom avanti e indietro.

![Area di lavoro grafica](media/automation-graphical-authoring-intro/runbook-canvas-controls.png)

### Controllo Library
Il controllo Library consente di selezionare [attività](#activities) da aggiungere al Runbook. È possibile aggiungerle al canvas, dove vengono connesse ad altre attività. Il canvas include quattro sezioni descritte nella tabella seguente.

| Sezione | Descrizione |
|:--- |:--- |
| Cmdlets |Include tutti i cmdlet che possono essere usati nel Runbook. I cmdlet sono organizzati per modulo. Saranno disponibili tutti i moduli installati nell'account di Automazione. |
| Runbook |Include i runbook dell'account di Automazione. Questi runbook possono essere aggiunti al canvas per essere usati come runbook figlio. Vengono visualizzati solo i runbook dello stesso tipo di memoria centrale come il runbook in corso di modifica; per i runbook grafici vengono visualizzati solo i runbook basati su PowerShell, mentre per i runbook grafici del flusso di lavoro PowerShell vengono visualizzati solo i runbook basati sul flusso di lavoro PowerShell. |
| Asset |Include gli [asset di automazione](http://msdn.microsoft.com/library/dn939988.aspx) dell'account di automazione che è possibile usare nel Runbook. Quando si aggiunge un asset a un Runbook, viene aggiunta un'attività flusso di lavoro che ottiene l'asset selezionato. Nel caso di attività di variabile, è possibile selezionare se aggiungere un'attività per ottenere la variabile o per impostarla. |
| Runbook Control |Include attività di controllo di Runbook che è possibile usare nel Runbook corrente. Una *giunzione* accetta più input e attende che vengano completati tutti prima di continuare il flusso di lavoro. Un'attività *codice* esegue una o più righe del codice PowerShell o del flusso di lavoro PowerShell a seconda del tipo di runbook grafico. È possibile usare questa attività per il codice personalizzato o per funzionalità difficili da ottenere con altre attività. |

### Controllo Configuration
Il Controllo della configurazione è il luogo in cui è possibile fornire i dettagli per un oggetto selezionato nel canvas. Le proprietà disponibili in questo controllo dipendono dal tipo di oggetto selezionato. Quando si seleziona un'opzione nel controllo Configuration, verranno visualizzati pannelli aggiuntivi per specificare altre informazioni.

### Controllo Test
Il controllo Test non viene visualizzato al primo avvio dell'editor grafico. Viene aperto quando si [testa un Runbook grafico](#graphical-runbook-procedures) in modo interattivo.

## Procedure relative a Runbook grafici
### Esportazione e importazione di un runbook con interfaccia grafica
È possibile esportare solo la versione pubblicata di un runbook con interfaccia grafica. Se il runbook non è ancora stato pubblicato, il pulsante **esportazione pubblicato** è disabilitato. Quando si fa clic sul pulsante **Esportazione pubblicata**, il runbook viene scaricato nel computer locale. Il nome del file corrisponde al nome del runbook con un estensione *graphrunbook*.

![Esportare pubblicazione](media/automation-graphical-authoring-intro/runbook-export.png)

È possibile importare un file di runbook grafico o di runbook grafico del flusso di lavoro PowerShell selezionando l'opzione **Importa** quando si aggiunge un runbook. Quando si seleziona il file da importare, è possibile mantenere lo stesso **Nome** o fornirne uno nuovo. Il campo Tipo di runbook visualizza il tipo di runbook dopo aver valutato il file selezionato e, se si tenta di selezionare un tipo diverso che non risulta corretto, viene visualizzato un messaggio che informa dell'esistenza di potenziali conflitti che potrebbero causare errori di sintassi durante la conversione.

![Importare runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### Test di un Runbook grafico
È possibile testare la versione bozza di un Runbook nel portale di Azure lasciando la versione pubblicata del Runbook invariata oppure testare un nuovo Runbook prima che venga pubblicato. In questo modo è possibile verificare che il Runbook funzioni correttamente prima di sostituire la versione pubblicata. Quando si testa un Runbook, viene eseguito il Runbook in versione bozza e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma l'output viene visualizzato nel riquadro di output del test.

Per aprire il controllo Test per un Runbook, aprire il Runbook per la modifica e quindi fare clic sul pulsante **Test pane**.

![Pulsante Test pane](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

Il controllo Test richiederà di specificare eventuali parametri di input e sarà possibile avviare il Runbook facendo clic sul pulsante **Start**.

![Pulsanti del controllo Test](media/automation-graphical-authoring-intro/runbook-test-start.png)

### Pubblicazione di un Runbook grafico
Ogni Runbook in Automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza è pronta per essere disponibile, è possibile pubblicarla sovrascrivendo così la versione pubblicata.

È possibile pubblicare un Runbook grafico aprendolo per la modifica e quindi facendo clic sul pulsante **Publish**.

![Pulsante Publish](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Un Runbook non ancora pubblicato è con stato **New**. Quando viene pubblicato, lo stato cambia in **Published**. Se si modifica il Runbook dopo la pubblicazione e le versioni bozza e pubblicata sono diverse, il Runbook risulta con stato **In edit**.

![Stati del Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

È anche possibile ripristinare la versione pubblicata di un Runbook. In questo modo verranno rimosse le modifiche apportate dopo l'ultima pubblicazione e la versione bozza del Runbook verrà sostituita con la versione pubblicata.

![Pulsante Revert to published](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)

## Attività
Le attività sono i blocchi predefiniti di un Runbook. Un'attività può essere un cmdlet di PowerShell, un Runbook figlio o un'attività flusso di lavoro. Per aggiungere un'attività al Runbook, fare clic con il pulsante destro del mouse sul controllo Library e scegliere **Add to canvas**. È quindi possibile fare clic e trascinare l'attività per posizionarla in un punto qualsiasi nel canvas desiderato. La posizione dell'attività sul canvas non influirà in alcun modo sul funzionamento del Runbook. È possibile modificare il layout del Runbook nel modo più adatto per consentire di visualizzarne il funzionamento.

![Add to canvas](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Selezionare l'attività nel canvas per configurare le relative proprietà e i relativi parametri nel pannello Configuration. È possibile modificare il campo **Label** dell'attività specificando un nome descrittivo per l'utente. Il cmdlet originale è ancora in esecuzione, si sta semplicemente modificando il nome visualizzato che verrà usato nell'editor grafico. L'etichetta deve essere univoca all'interno del Runbook.

### Set di parametri
Un set di parametri definisce i parametri obbligatori e facoltativi che accetteranno i valori per un cmdlet specifico. Tutti i cmdlet dispongono di almeno un set di parametri, altri dispongono di più set di parametri. Se un cmdlet dispone di più set di parametri, sarà necessario selezionare quello da usare prima di configurare i parametri. I parametri che è possibile configurare dipendono da set di parametri scelto. È possibile modificare il set di parametri usato da un'attività selezionando **Parameter Set** e quindi un altro set. In questo caso, i valori dei parametri configurati vengono persi.

Nell'esempio seguente il cmdlet Get-AzureRmVM dispone di tre set di parametri. Non è possibile configurare i valori dei parametri fino a quando non si seleziona uno dei set di parametri. Il set di parametri ListVirtualMachineInResourceGroupParamSet restituisce tutte le macchine virtuali in un gruppo di risorse e dispone di un solo parametro facoltativo. Il set GetVirtualMachineInResourceGroupParamSet consente di specificare la macchina virtuale da restituire e dispone di due parametri obbligatori e di un parametro facoltativo.

![Set di parametri](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### Valori dei parametri
Quando si specifica un valore per un parametro, è necessario selezionare un'origine dati per determinare come verrà specificato il valore. Le origini dati disponibili per un determinato parametro dipenderanno dai valori validi per il parametro. Null ad esempio non sarà disponibile come opzione di un parametro che non consente valori Null.

| Origine dati | Descrizione |
|:--- |:--- |
| Constant Value |Digitare un valore per il parametro. Disponibile solo per i tipi di dati seguenti: Int32, Int64, String, Boolean, DateTime, Switch. |
| Activity Output |Output di un'attività che precede l'attività corrente nel flusso di lavoro. Verranno elencate tutte le attività valide. Selezionare l'attività per usare il relativo output per il valore del parametro. Se l'attività restituisce un oggetto con più proprietà, è possibile digitare il nome della proprietà dopo aver selezionato l'attività. |
| Input di runbook |Selezionare un parametro input di Runbook come input per il parametro dell'attività. |
| Asset della variabile |Selezionare una variabile di Automazione come input. |
| Asset di credenziali |Selezionare una credenziale di Automazione come input. |
| Asset del certificato |Selezionare un certificato di Automazione come input. |
| Asset di connessione |Selezionare una connessione di Automazione come input. |
| PowerShell Expression |Specificare un'[espressione di PowerShell](#powershell-expressions) semplice. L'espressione verrà valutata prima dell'attività e il risultato verrà usato per il valore del parametro. È possibile usare variabili per fare riferimento all'output di un'attività o un parametro di input di Runbook. |
| Non configurato |Cancella qualsiasi valore precedentemente configurato. |

#### Parametri aggiuntivi facoltativi
Per tutti i cmdlet è possibile specificare parametri aggiuntivi. Si tratta di parametri comuni di PowerShell o di altri parametri personalizzati. Viene visualizzata una casella di testo in cui è possibile specificare parametri usando la sintassi di PowerShell. Per usare ad esempio il parametro comune **Verbose**, specificare **"-Verbose: $True"**.

### Ripetere l'attività
**Comportamento in caso di nuovo tentativo** consente di eseguire un'attività più volte e come ciclo, finché non viene soddisfatta una determinata condizione. È possibile usare questa funzionalità per le attività da eseguire più volte, soggette a errori e che potrebbero richiedere più di un tentativo per riuscire o per testare le informazioni di output dell'attività al fine di controllarne la validità dei dati.

Quando si abilita la ripetizione dei tentativi per un'attività, è possibile impostare un ritardo e una condizione. Il ritardo è il tempo, espresso in secondi o minuti, che il runbook attenderà prima di eseguire nuovamente l'attività. Se non viene specificato un ritardo, l'attività verrà eseguita immediatamente dopo il completamento.

![Ritardo di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/retry-delay.png)

La condizione di ripetizione dei tentativi è un'espressione di PowerShell che viene valutata dopo ogni esecuzione dell'attività. Se l'espressione restituisce True, l'attività viene eseguita di nuovo. Se l'espressione restituisce False, l'attività non viene eseguita di nuovo e il runbook passa all'attività successiva.

![Ritardo di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/retry-condition.png)

La condizione di ripetizione dei tentativi può usare una variabile denominata $RetryData che fornisce l'accesso alle informazioni sulla ripetizione dei tentativi dell'attività. Questa variabile include le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| NumberOfAttempts |Numero di volte in cui l'attività è stata eseguita. |
| Output |Output dell'ultima esecuzione dell'attività. |
| TotalDuration |Tempo trascorso dal primo avvio dell'attività. |
| StartedAt |Ora in formato UTC del primo avvio dell'attività. |

Di seguito sono riportati alcuni esempi di condizioni per la ripetizione dei tentativi dell'attività.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10 

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1 

    # Run the activity repeatedly until 2 minutes has elapsed. 
    $RetryData.TotalDuration.TotalMinutes -ge 2

Dopo aver configurato la condizione di ripetizione dei tentativi per un'attività, l'attività include due segnali visivi come promemoria per l'utente. Uno viene visualizzato all'interno dell'attività e l'altro durante il controllo della configurazione dell'attività.

![Segnali visivi di ripetizione dei tentativi di attività](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### Controllo Workflow Script
Un Controllo codice è un'attività speciale che accetta lo script PowerShell o del flusso di lavoro PowerShell a seconda del tipo di runbook grafico che si sta creando per offrire funzionalità che altrimenti potrebbero non essere disponibili. Non può accettare parametri, ma può usare le variabili per parametri di output di attività e di input di Runbook. Gli output dell'attività vengono aggiunti al bus di dati, a meno che non dispongano di alcun collegamento in uscita, nel qual caso vengono aggiunti all'output del Runbook.

Il codice seguente ad esempio esegue calcoli di data usando una variabile di input di Runbook denominata $NumberOfDays. Invia quindi una data/ora calcolata come output che verrà usato dalle attività successive nel Runbook.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## Collegamenti e flusso di lavoro
Un **collegamento** in un Runbook grafico connette due attività. Viene visualizzato nel canvas come una freccia che punta dall'attività di origine verso l'attività di destinazione. Le attività vengono eseguite nella direzione della freccia, con l'attività di destinazione che inizia dopo il completamento dell'attività di origine.

### Creare un collegamento
Creare un collegamento tra due attività selezionando l'attività di origine e facendo clic sul cerchio nella parte inferiore della forma. Trascinare la freccia sull'attività di destinazione e rilasciare.

![Creare un collegamento](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Selezionare il collegamento per configurare le relative proprietà nel pannello Configuration, incluso il tipo di collegamento, che viene descritto nella tabella seguente.

| Tipo di collegamento | Descrizione |
|:--- |:--- |
| Pipeline |L'attività di destinazione viene eseguita una volta per ogni oggetto restituito dall'attività di origine. L'attività di destinazione non viene eseguita se l'attività di origine non genera alcun output. L'output dell'attività di origine è disponibile come oggetto. |
| Sequenza |L'attività di destinazione viene eseguita una sola volta. Riceve una matrice di oggetti dall'attività di origine. L'output dell'attività di origine è disponibile come matrice di oggetti. |

### Attività di inizio
Un Runbook grafico inizia con le attività che non dispongono di un collegamento in ingresso. Si tratta in genere di una sola attività, che svolge la funzione di attività iniziale per il Runbook. Se esistono più attività che non dispongono di un collegamento in ingresso, il Runbook si avvierà eseguendole in parallelo. Seguirà quindi i collegamenti per eseguire altre attività man mano che vengono completate.

### Condizioni
Se si specifica una condizione su un collegamento, l'attività di destinazione verrà eseguita solo se la condizione restituisce true. Verrà usata in genere una variabile $ActivityOutput in una condizione per recuperare l'output dell'attività di origine.

Per un collegamento di tipo pipeline, si specifica una condizione per un singolo oggetto e la condizione viene valutata per ogni output dell'oggetto da parte dell'attività di origine. L'attività di destinazione viene quindi eseguita per ogni oggetto che soddisfa la condizione. Con un'attività di origine di Get-AzureRmVm ad esempio è possibile usare la sintassi seguente in modo che un collegamento di tipo pipeline condizionale recuperi solo le macchine virtuali nel gruppo di risorse denominato *Group1*.

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

Per un collegamento di tipo sequenza, la condizione viene valutata solo dopo la restituzione di una singola matrice contenente tutti gli oggetti restituiti dall'attività di origine. Per questo motivo, un collegamento di tipo sequenza non può essere usato per il filtro come un collegamento di tipo pipeline, ma determina semplicemente se l'attività successiva viene o non viene eseguita. Si consideri ad esempio il seguente set di attività nel runbook Start VM.<br> ![Collegamento condizionale con sequenze](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br> Esistono tre collegamenti di tipo sequenza diversi che verificano i valori specificati su due parametri di input runbook che rappresentano il nome della macchina virtuale e il nome del gruppo di risorse per determinare l'azione appropriata da eseguire: avviare una singola macchina virtuale, avviare tutte le macchine virtuali nel gruppo di risorse o avviare tutte le macchine virtuali in una sottoscrizione. Per il collegamento di tipo sequenza tra Connect to Azure e Get single VM, questa è la logica della condizione:

    <# 
    Both VMName and ResourceGroupName runbook input parameters have values 
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

Quando si usa un collegamento condizionale, i dati disponibili dall'attività di origine per altre attività nel ramo vengono filtrati dalla condizione. Se un'attività è all'origine di più collegamenti, i dati disponibili per le attività in ogni ramo variano in base alla condizione nel collegamento connesso a tale ramo.

L'attività **Start-AzureRmVm** del runbook seguente ad esempio recupera tutte le macchine virtuali. Dispone di due collegamenti condizionali. Il primo collegamento condizionale usa l'espressione *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true* per eseguire la filtrazione se l'attività Start-AzureRmVm è stata completata correttamente. Il secondo collegamento usa l'espressione *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true* per eseguire la filtrazione se l'attività Start-AzureRmVm non è riuscita ad avviare la macchina virtuale.

![Esempio di collegamento condizionale](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Qualsiasi attività che segue il primo collegamento e usa l'output dell'attività da Get-AzureVM recupera solo le macchine virtuali che erano avviate al momento dell'esecuzione di Get-AzureVM. Un'attività che segue il secondo collegamento recupera solo le macchine virtuali che erano arrestate al momento dell'esecuzione Get-AzureVM. Le attività che seguono il terzo collegamento recuperano tutte le macchine virtuali, indipendentemente dallo stato di esecuzione.

### Giunzioni
Una giunzione è un'attività speciale che attende il completamento di tutti i rami in ingresso. In questo modo è possibile eseguire più attività in parallelo e assicurarsi che siano state tutte completate prima di procedere.

Mentre una giunzione può disporre di un numero illimitato di collegamenti in entrata, solo uno di essi può essere una pipeline. Il numero di collegamenti di tipo sequenza in ingresso non prevede vincoli. Sarà possibile creare la giunzione con più collegamenti di tipo pipeline in ingresso e salvare il Runbook, ma l'esecuzione restituirà esito negativo.

L'esempio seguente fa parte di un Runbook che avvia un set di macchine virtuali e scarica contemporaneamente patch da applicare a tali macchine. Viene usata una giunzione per verificare che entrambi i processi vengano completati prima che il Runbook continui.

![Giunzione](media/automation-graphical-authoring-intro/runbook-junction.png)

### Cicli
Un ciclo si verifica quando un'attività di destinazione si collega alla relativa attività di origine o a un'altra attività che alla fine si collega all'origine. I cicli non sono attualmente consentiti nella creazione grafica. Se un Runbook dispone di un ciclo, verrà salvato correttamente, ma riceverà un errore durante l'esecuzione.

![Ciclo](media/automation-graphical-authoring-intro/runbook-cycle.png)

### Condivisione di dati tra le attività
Tutti i dati restituiti da un'attività con un collegamento in uscita vengono scritti nel *bus di dati* del Runbook. Le attività nel Runbook possono usare i dati del bus di dati per popolare valori di parametri o includere codice di script. Un'attività può accedere all'output di qualsiasi attività precedente nel flusso di lavoro.

I dati vengono scritti nel bus di dati secondo il tipo di collegamento dell'attività. Per una **pipeline**, i dati vengono restituiti come oggetti multipli. Per una **sequenza**, i dati vengono restituiti come matrice. Se è presente un solo valore, questo verrà restituito come matrice a elemento singolo.

È possibile accedere ai dati nel bus di dati procedendo in uno dei due modi seguenti. Il primo prevede l'uso di un'origine dati **Activity Output** per popolare un parametro di un'altra attività. Se l'output è un oggetto, è possibile specificare una singola proprietà.

![Output di attività](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

È anche possibile recuperare l'output di un'attività in un'origine dati **PowerShell Expression** o da un'attività **Workflow Script** con una variabile ActivityOutput. Se l'output è un oggetto, è possibile specificare una singola proprietà. Le variabili ActivityOutput usano la sintassi seguente.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName 

### Checkpoint
È possibile impostare [Checkpoint](automation-powershell-workflow.md#checkpoints) in un runbook grafico del flusso di lavoro PowerShell selezionando *Checkpoint per runbook* in qualsiasi attività. In questo modo viene impostato un checkpoint dopo l'esecuzione dell'attività.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

I checkpoint sono abilitati solo nei runbook grafici del flusso di lavoro PowerShell e non sono disponibili nei runbook grafici. Se il runbook usa i cmdlet di Azure, è consigliabile eseguire qualsiasi attività con checkpoint con un Add-AzureRMAccount, nel caso in cui il runbook venga sospeso e riavviato da questo checkpoint in un ruolo di lavoro diverso.

## Autenticazione per le risorse di Azure
La maggior parte dei runbook di Automazione di Azure che gestisce risorse di Azure richiede l'autenticazione ad Azure. La nuova funzionalità di [account RunAs](automation-sec-configure-azure-runas-account.md), anche detta entità servizio, costituisce il metodo predefinito per accedere alle risorse di Azure Resource Manager nella sottoscrizione con i runbook di Automazione. È possibile aggiungere questa funzionalità a un runbook grafico tramite l'aggiunta dell'asset di connessione **AzureRunAsConnection**, ovvero usando il cmdlet [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) di PowerShell e il cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx)nel canvas. Questo è illustrato nell'esempio seguente.<br>![Attività di autenticazione RunAs](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br> L'attività di connessione Get Run As, ad esempio Get-AutomationConnection, viene configurata con un'origine dei dati del valore costante denominata AzureRunAsConnection.<br>![Configurazione della connessione RunAs](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br> L'attività successiva, Add-AzureRmAccount, aggiunge l'account RunAs autenticato per l'uso con il runbook.<br> ![Set Add-AzureRmAccount Parameter](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br> Per i parametri **APPLICATIONID**, **CERTIFICATETHUMBPRINT** e **TENANTID** è necessario specificare il nome della proprietà per il percorso del campo perché l'attività restituisca un oggetto con più proprietà. In caso contrario, quando si esegue il runbook, il tentativo di autenticazione avrà esito negativo. Questi sono i requisiti minimi necessari per l'autenticazione del runbook con l'account RunAs.

Per mantenere la compatibilità con le versioni precedenti per i sottoscrittori che hanno creato un account di Automazione tramite un [account utente di Azure AD](automation-sec-configure-aduser-account.md) per la gestione delle risorse di Azure Service Management (ASM) o Azure Resource Manager, il metodo per l'autenticazione è il cmdlet Add-AzureAccount con un [asset di credenziali](http://msdn.microsoft.com/library/dn940015.aspx) che rappresenta un utente di Active Directory con accesso all'account Azure.

È possibile aggiungere questa funzionalità a un Runbook grafico mediante l'aggiunta di un asset credenziali nel canvas seguito da un'attività Add-AzureAccount. Add-AzureAccount usa l'attività credenziali come input. Questo è illustrato nell'esempio seguente.

![Attività di autenticazione](media/automation-graphical-authoring-intro/authentication-activities.png)

È necessario eseguire l'autenticazione all'inizio del Runbook e dopo ogni checkpoint. Questo significa aggiungere un'attività Add-AzureAccount dopo qualsiasi attività Checkpoint-Workflow. Non è necessaria un'attività credenziali aggiuntiva poiché è possibile usare la stessa.

![Output di attività](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Input e output di Runbook
### Input di Runbook
Un Runbook può richiedere l'input di un utente che avvia il Runbook tramite il portale di Azure o di un altro Runbook se quello corrente viene usato come Runbook figlio. Se ad esempio si dispone di un Runbook che crea una macchina virtuale, potrebbe essere necessario specificare informazioni come il nome della macchina virtuale e altre proprietà ogni volta che si avvia il Runbook.

È possibile accettare l'input per un Runbook definendo uno o più parametri di input. Specificare valori per questi parametri ogni volta che viene avviato il Runbook. Se si avvia un Runbook con il portale di Azure, verrà chiesto di specificare valori per ognuno dei parametri di input del Runbook.

È possibile accedere ai parametri di input per un Runbook facendo clic sul pulsante **Input and output** sulla barra degli strumenti del Runbook.

![Input e output di Runbook](media/automation-graphical-authoring-intro/runbook-edit-input-output.png)

Verrà aperto il controllo **Input and Output**, in cui è possibile modificare un parametro di input esistente o crearne uno nuovo facendo clic su **Add input**.

![Add input](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Ogni parametro di input è definito dalle proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Name |Nome univoco del parametro. Può contenere solo caratteri alfanumerici e non può contenere spazi. |
| Description |Descrizione facoltativa del parametro di input. |
| Type |Tipo di dati previsto per il valore del parametro. Il portale di Azure visualizzerà un controllo appropriato per il tipo di dati per ogni parametro quando viene richiesto l'input. |
| Obbligatorio |Specifica se è necessario specificare un valore per il parametro. Non è possibile avviare il Runbook se non si specifica un valore per ogni parametro obbligatorio che non dispone di un valore predefinito. |
| Default Value |Specifica quale valore viene usato per il parametro se non ne viene specificato uno. Può essere Null o un valore specifico. |

### Output del Runbook
I dati creati da un'attività che non dispone di un collegamento in uscita verranno aggiunti all'[output del Runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). L'output viene salvato con il processo di Runbook ed è disponibile per un Runbook padre quando il Runbook viene usato come Runbook figlio.

## Espressioni PowerShell
Uno dei vantaggi della creazione grafica è la possibilità di creare un runbook con una minima conoscenza di PowerShell. Attualmente, è necessario avere una discreta conoscenza di PowerShell per la compilazione di determinati [valori dei parametri](#activities) e per l'impostazione di [condizioni di collegamento](#links-and-workflow). Questa sezione fornisce una rapida introduzione alle espressioni di PowerShell per quegli utenti che potrebbero non avere familiarità con esso. I dettagli completi di PowerShell sono disponibili all'indirizzo [Scripting con Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx).

### Origine dati delle espressioni PowerShell
È possibile utilizzare un'espressione di PowerShell come origine dati per popolare il valore di un [parametro attività](#activities) con i risultati di un codice PowerShell. Potrebbe trattarsi di una singola riga di codice che esegue una funzione semplice o più righe che eseguono una logica complessa. L'output di un comando che non è assegnato a una variabile è l'output per il valore del parametro.

Il comando seguente, ad esempio, avrebbe generato la data corrente.

    Get-Date

I seguenti comandi compilano una stringa della data corrente e l’assegnano a una variabile. Il contenuto della variabile viene quindi inviato all'output

    $string = "The current date is " + (Get-Date)
    $string

I seguenti comandi valutano la data corrente e restituiscono una stringa che indica se il giorno corrente rappresenta un fine settimana o un giorno feriale.

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### Output di attività
Per utilizzare l'output da una precedente attività del runbook, utilizzare la variabile $ActivityOutput con la sintassi seguente.

    $ActivityOutput['Activity Label'].PropertyName

Ad esempio, si potrebbe avere un'attività con una proprietà che richiede il nome di una macchina virtuale e in questo caso è possibile utilizzare l'espressione seguente.

    $ActivityOutput['Get-AzureVm'].Name

Se la proprietà che richiede la macchina virtuale è un oggetto anziché una proprietà, sarebbe necessario restituire l'intero oggetto utilizzando la sintassi seguente.

    $ActivityOutput['Get-AzureVm']

È inoltre possibile utilizzare l'output di un'attività in un'espressione più complessa, come quella illustrata di seguito che consente di concatenare testo al nome della macchina virtuale.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### Condizioni
Utilizzare [operatori di confronto](https://technet.microsoft.com/library/hh847759.aspx) per confrontare i valori o determinare se un valore corrisponde a un modello specificato. Un confronto restituisce un valore $true o $false.

Ad esempio, la condizione seguente determina se la macchina virtuale da un'attività denominata *Get-AzureVM* è attualmente nello stato *arrestato*.

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

La seguente condizione verifica se la stessa macchina virtuale è in uno stato diverso da *arrestato*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

È possibile aggiungere più condizioni utilizzando un [operatore logico](https://technet.microsoft.com/library/hh847789.aspx) ad esempio **- e** o **- o**. Ad esempio, la seguente condizione verifica se la stessa macchina virtuale dell'esempio precedente è nello stato *arrestato* o *in arresto*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### Tabelle hash
Le [Tabelle hash](http://technet.microsoft.com/library/hh847780.aspx) sono coppie nome/valore utili per la restituzione di un set di valori. Le proprietà per determinate attività potrebbero prevedere una tabella hash anziché un valore semplice. È inoltre possibile visualizzare come utilizzare la tabella hash come dizionario.

Creare una tabella hash con la sintassi seguente. Una tabella hash può contenere qualsiasi numero di voci, ma ciascuna è definita da un nome e valore.

    @{ <name> = <value>; [<name> = <value> ] ...}

Ad esempio, l'espressione seguente crea una tabella hash da utilizzare nell'origine dati per un parametro di attività che prevede una tabella hash con i valori per una ricerca in internet.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

Nell'esempio seguente viene utilizzato l'output di un'attività denominata *Get Twitter Connection* per popolare una tabella hash.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## Passaggi successivi
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md) 
* Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md)
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
* Per informazioni su come eseguire l'autenticazione usando l'account RunAs di Automazione, vedere l'articolo relativo alla [configurazione di un account RunAs da Azure](automation-sec-configure-azure-runas-account.md)

<!---HONumber=AcomDC_0608_2016-->