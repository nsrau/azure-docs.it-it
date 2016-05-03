<properties
    pageTitle="Il primo runbook del flusso di lavoro di PowerShell in Automazione di Azure | Microsoft Azure"
    description="Esercitazione in cui viene illustrata la creazione, il test e la pubblicazione di un semplice runbook testuale con flusso di lavoro PowerShell."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/21/2016"
    ms.author="magoedte;bwren"/>

# Il primo runbook del flusso di lavoro PowerShell

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Questa esercitazione illustra la creazione di un [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powerShell-workflow-runbooks) in Automazione di Azure. Si inizierà con un runbook semplice che sarà testato e pubblicato, quindi verrà illustrato come tenere traccia dello stato del processo del runbook. Si modificherà quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si renderà quindi il runbook più affidabile aggiungendo i relativi parametri.

## Prerequisiti

Per completare questa esercitazione, sono necessari gli elementi seguenti.

-	Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i benefici per i sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure <a href="/pricing/free-account/" target="_blank">[iscriversi per ottenere un account gratuito](https://azure.microsoft.com/free/).
-	[Account di Automazione](automation-security-overview.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
-	Macchina virtuale di Azure. Si arresterà e si avvierà la macchina virtuale in modo che non sia di produzione.

## Passaggio 1: Creare nuovo runbook

Si inizierà creando un runbook semplice che restituisce il testo *Hello World*.

1.	Nel portale di Azure aprire l'account di automazione. La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).
2.	Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.<br> ![Controllo Runbook](media/automation-first-runbook-textual/runbooks-control.png)
3.	Creare un nuovo runbook facendo clic sul pulsante **Aggiungi Runbook** e quindi su **Crea un nuovo runbook**.
4.	Denominare il runbook *MyFirstRunbook-Workflow*.
5.	In questo caso, è necessario creare un[runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powerShell-workflow-runbooks)basato sul flusso di lavoro PowerShell pertanto selezionare **flusso di lavoro Powershell** per **tipo di Runbook**.<br> ![Nuovo runbook](media/automation-first-runbook-textual/new-runbook.png)
6.	Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa procedura dettagliata, si digiterà direttamente nel runbook.

1.	Il runbook è attualmente vuoto con solo la parola chiave richiesta *workflow*, il nome del runbook e le parentesi graffe che racchiuderanno l'intero flusso di lavoro.<br> ![Controllo Runbook](media/automation-first-runbook-textual/empty-runbook.png)
2.	Digitare *Write-Output "Hello World".* tra parentesi graffe. <br> ![Hello World](media/automation-first-runbook-textual/hello-world.png)
3.	Salvare il runbook facendo clic su **Salva**.<br> ![Salvataggio del runbook](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1.	Fare clic su **Pannello di test** per aprire il pannello di test.<br>![Riquadro Test](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2.	Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3.	Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato. Lo stato del processo verrà avviato come *In coda* per indicare che è in attesa della disponibilità di un thread di lavoro del runbook nel cloud. Lo stato passerà quindi a *Avvio in corso* quando un thread di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
4.	Al termine del processo del runbook, viene visualizzato l'output. In questo caso, dovrebbe essere visualizzato *Hello World*.<br> ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5.	Chiudere il riquadro di test per tornare all'area di disegno.

## Passaggio 4: Pubblicare e avviare il runbook

Il runbook appena creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1.	Fare clic su**Pubblica** per pubblicare il runbook, quindi **Sì** quando richiesto.<br> ![Publish](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2.	Se ora si scorre verso sinistra per visualizzare il runbook nel pannello **Runbook**, come **Stato di creazione** viene visualizzato**Pubblicato**.
3.	Scorrere verso destra per visualizzare il pannello **MyFirstRunbook-Workflow**. Le opzioni nella parte superiore consentono di avviare il runbook, pianificarlo per avviarlo in qualsiasi momento in futuro o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
4.	Per avviare semplicemente il runbook, fare clic su **Avvia** e quindi su **Sì** quando richiesto.<br>![Avvia runbook](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5.	Viene aperto un riquadro del processo per il processo del runbook appena creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare l'avanzamento del processo.
6.	Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.<br> ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-summary.png)
7.	Quando lo stato del runbook risulta *Completato* fare clic su **Output**. Viene aperto il pannello Output dove si può vedere il testo *Hello World*.<br> ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-output.png)  
8.	Chiudere il riquadro Output.
9.	Fare clic su **Flussi** per aprire il riquadro dei flussi per il processo del runbook. Nel flusso di output dovrebbe essere visibile solo *Hello World*, ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi.<br> ![Riepilogo dei processi](media/automation-first-runbook-textual/job-pane-streams.png)
10.	Chiudere il riquadro dei flussi e il riquadro dei processi per tornare al riquadro MyFirstRunbook.
11.	Fare clic su**Processi** per aprire il pannello dei processi per questo runbook. Sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.<br> ![Processi](media/automation-first-runbook-textual/runbook-control-jobs.png)
12.	È possibile fare clic su questo processo per aprire lo stesso riquadro del processo visualizzato quando è stato avviato il runbook. In questo modo è possibile tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Sarà tuttavia in grado di eseguire questa operazione solo dopo aver fatto in modo che esegua l'autenticazione con le credenziali indicate nei [prerequisiti](#prerequisites). A questo scopo si userà il cmdlet **Add-AzureAccount**.

1.	Aprire l'editor di testo facendo clic su **Modifica** nel pannello MyFirstRunbook-Workflow<br>. ![Modificare il runbook](media/automation-first-runbook-textual/runbook-toolbar-edit.png)
2.	Non è più necessaria la riga **Write-Output**, quindi andare avanti ed eliminarla.
3.	Posizionare il cursore su una riga vuota tra parentesi graffe.
4.	Nel controllo libreria, espandere**Assets**e quindi**Credentials**.
5.	Fare clic con il pulsante destro sulle credenziali e fare clic su **Aggiungi all'area di disegno**. Questo aggiunge un’attività **Get-AutomationPSCredential** per le credenziali.
6.	Davanti a **Get-AutomationPSCredential**, digitare *$Credential =* per assegnare le credenziali a una variabile.
7.	Nella riga successiva, digitare *Add-AzureAccount-Credential $Credential*. <br> ![Autentica](media/automation-first-runbook-textual/authentication.png)
8.	Fare clic sul **Pannello di test** in modo da testare il runbook.
9.	Fare clic su **Avvia** per avviare il test. Una volta che viene completato, si dovrebbe ricevere un output simile al seguente che restituisce le informazioni per l'utente nella credenziale. Questo conferma che la credenziale è valida.<br> ![Autentica](media/automation-first-runbook-textual/authentication-test.png)

## Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure, è possibile gestire le risorse. Si aggiungerà un comando per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome verrà hardcoded nel cmdlet.

1.	Dopo *Add-AzureAccount* digitare *Start-AzureVM-nome 'VMName' - ServiceName 'VMServiceName'* specificando il nome e il nome del servizio della macchina virtuale da avviare. <br> ![Autentica](media/automation-first-runbook-textual/start-azurevm.png)
2.	Salvare il runbook e poi fare clic su **Pannello di Test** in modo da poterne eseguire il test.
3.	Fare clic su **Avvia** per avviare il test. Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.

## Passaggio 7: Aggiungere un parametro di input al runbook

Ora il runbook avvia la macchina virtuale specificata nel runbook, ma sarebbe più utile se si potesse specificare la macchina virtuale quando si avvia il runbook. Per fornire questa funzionalità, ora si aggiungeranno dei parametri di input al runbook.

1.	Aggiungere parametri per *VMName* e *VMServiceName* al runbook e utilizzare queste variabili con il cmdlet **Start-AzureVM** come illustrato nell'immagine seguente. <br> ![Autentica](media/automation-first-runbook-textual/params.png)
2.	Salvare il runbook e aprire il riquadro Test. Si noti che ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.
3.	Chiudere il riquadro Test.
4.	Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
5.	Arrestare la macchina virtuale avviata nel passaggio precedente.
6.	Fare clic su **Avvia** per avviare il runbook. Digitare **VMName** e **VMServiceName** per la macchina virtuale da avviare.<br> ![Avviare il Runbook](media/automation-first-runbook-textual/start-runbook-input-params.png)

7.	Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## Articoli correlati

-	[Il primo runbook grafico](automation-first-runbook-graphical.md)
-	[Il primo runbook PowerShell](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0427_2016-->