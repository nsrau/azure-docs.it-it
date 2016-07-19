<properties
    pageTitle="Il primo runbook PowerShell in Automazione di Azure | Microsoft Azure"
    description="Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook PowerShell."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="Azure PowerShell, esercitazione sugli script di PowerShell, automazione di PowerShell"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/02/2016"
    ms.author="magoedte;sngun"/>

# Il primo runbook PowerShell

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)

Questa esercitazione illustra la creazione di un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) in Automazione di Azure. Si inizierà con un runbook semplice che sarà testato e pubblicato, quindi verrà illustrato come tenere traccia dello stato del processo del runbook. Si modificherà quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si renderà quindi il runbook più affidabile aggiungendo i relativi parametri.

## Prerequisiti

Per completare questa esercitazione, sono necessari gli elementi seguenti.

-	sottoscrizione Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i benefici per i sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure <a href="/pricing/free-account/" target="_blank">[iscriversi per ottenere un account gratuito](https://azure.microsoft.com/free/).
-	[Account di Automazione](automation-security-overview.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
-	Macchina virtuale di Azure. Si arresterà e si avvierà la macchina virtuale in modo che non sia di produzione.

## Passaggio 1: Creare nuovo runbook

Si inizierà creando un runbook semplice che restituisce il testo *Hello World*.

1.	Nel portale di Azure aprire l'account di automazione. La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).
2.	Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook. ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)
3.	Creare un nuovo runbook facendo clic sul pulsante **Aggiungi Runbook** e quindi su **Crea un nuovo runbook**.
4.	Denominare il runbook *MyFirstRunbook-PowerShell*.
5.	In questo caso si sta creando un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks), quindi selezionare **Powershell** per **Tipo di Runbook**.![Tipo di runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)
6.	Fare clic su **Crea** per creare il runbook e aprire l'editor di testo.

## Passaggio 2: - aggiungere un codice al runbook

È possibile digitare il codice direttamente nel runbook, oppure è possibile selezionare i cmdlet, i runbook e le risorse dal controllo della libreria e aggiungerle al runbook con tutti i parametri correlati. Per questa procedura dettagliata, si digiterà direttamente nel runbook.

1.	Il runbook è attualmente vuoto, digitare *Write-Output "Hello World"*. ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)
2.	Salvare il runbook facendo clic su **Salva**. ![Pulsante Salva](media/automation-first-runbook-textual-powershell/automation-save-button.png)

## Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1.	Fare clic su **Pannello di test** per aprire il pannello di test. ![Riquadro Test](media/automation-first-runbook-textual-powershell/automation-testpane.png)
2.	Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3.	Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato. Lo stato del processo verrà avviato come *In coda* per indicare che è in attesa della disponibilità di un thread di lavoro del runbook nel cloud. Lo stato passerà quindi a *Avvio in corso* quando un thread di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.
4.	Al termine del processo del runbook, viene visualizzato l'output. In questo caso, dovrebbe essere visualizzato *Hello World*. ![Output del Riquadro test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)
5.	Chiudere il riquadro di test per tornare all'area di disegno.

## Passaggio 4: Pubblicare e avviare il runbook

Il runbook appena creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1.	Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto. ![Pulsante Publish](media/automation-first-runbook-textual-powershell/automation-publish-button.png)
2.	Se ora si scorre verso sinistra per visualizzare il runbook nel pannello **Runbook**, come **Stato di creazione** viene visualizzato**Pubblicato**.
3.	Scorrere verso destra per visualizzare il riquadro **MyFirstRunbook-PowerShell**. Le opzioni nella parte superiore consentono di avviare il runbook, visualizzarlo, pianificarlo per l'avvio in un momento successivo o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
4.	Si vuole solo avviare il runbook, di conseguenza fare clic su **Avvia** e quindi su **OK** all'apertura del pannello Avvia runbook. ![Pulsante Avvia](media/automation-first-runbook-textual-powershell/automation-start-button.png)
5.	Viene aperto un riquadro del processo per il processo del runbook appena creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare l'avanzamento del processo.
6.	Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook. ![Riepilogo dei processi](media/automation-first-runbook-textual-powershell/automation-job-summary.png)
7.	Quando lo stato del runbook risulta *Completato* fare clic su **Output**. Viene aperto il pannello Output dove si può vedere il testo *Hello World*. ![Output processo](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.	Chiudere il riquadro Output.
9.	Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo *Hello World*, ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi. ![Tutti i log](media/automation-first-runbook-textual-powershell/automation-alllogs.png)
10.	Chiudere il riquadro Flussi e il riquadro dei processi per tornare al riquadro MyFirstRunbook-PowerShell.
11.	Fare clic su**Processi** per aprire il pannello dei processi per questo runbook. Sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta. ![Elenco processi](media/automation-first-runbook-textual-powershell/automation-job-list.png)
12.	È possibile fare clic su questo processo per aprire lo stesso riquadro del processo visualizzato quando è stato avviato il runbook. In questo modo è possibile tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Sarà tuttavia in grado di eseguire questa operazione solo dopo aver fatto in modo che esegua l'autenticazione con le credenziali indicate nei [prerequisiti](#prerequisites). A questo scopo si userà il cmdlet **Add-AzureRmAccount**.

1.	Aprire l'editor di testo facendo clic su **Modifica** nel riquadro MyFirstRunbook-PowerShell. ![Modifica runbook](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)
2.	Non è più necessaria la riga **Write-Output**, quindi andare avanti ed eliminarla.
3.	Digitare o copiare e incollare il codice seguente che gestirà l'autenticazione con l'account RunAs di Automazione:

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```
<br>
4.	Fare clic sul **Pannello di test** in modo da testare il runbook.
5.	Fare clic su **Avvia** per avviare il test. Al termine, dovrebbe si dovrebbe ricevere l'output che mostra le informazioni di base sull'account. Questo conferma che le credenziali sono valide. <br> ![Autentica](media/automation-first-runbook-textual-powershell/runbook-auth-results.png)

## Passaggio 6 - aggiungere il codice per avviare una macchina virtuale

Ora che il runbook esegue l'autenticazione per la sottoscrizione di Azure, è possibile gestire le risorse. Si aggiungerà un comando per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome verrà hardcoded nel cmdlet.

1.	Dopo *Add-AzureRmAccount* digitare *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* specificando il nome e il nome del gruppo di risorse della macchina virtuale da avviare.

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID `$Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
     Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
     ```
<br>
2.	Salvare il runbook e poi fare clic su **Pannello di Test** in modo da poterne eseguire il test.
3.	Fare clic su **Avvia** per avviare il test. Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.

## Passaggio 7: Aggiungere un parametro di input al runbook

Ora il runbook avvia la macchina virtuale specificata nel runbook, ma sarebbe più utile se si potesse specificare la macchina virtuale quando si avvia il runbook. Per fornire questa funzionalità, ora si aggiungeranno dei parametri di input al runbook.

1.	Aggiungere parametri per *VMName* e *ResourceGroupName* al runbook e usare queste variabili con il cmdlet **Start-AzureRmVM** come nell'esempio seguente.

    ```
    Param(
       [string]$VMName,
       [string]$ResourceGroupName
    )
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationID `$Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
     Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
     ```
<br>
2.	Salvare il runbook e aprire il riquadro Test. Si noti che ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.
3.	Chiudere il riquadro Test.
4.	Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
5.	Arrestare la macchina virtuale avviata nel passaggio precedente.
6.	Fare clic su **Avvia** per avviare il runbook. Digitare **VMName** e **ResourceGroupName** per la macchina virtuale da avviare. ![Passare i parametri](media/automation-first-runbook-textual-powershell/automation-pass-params.png)
7.	Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## Differenze rispetto al flusso di lavoro PowerShell

I runbook PowerShell hanno lo stesso ciclo di vita, le stesse funzionalità e la stessa modalità di gestione dei runbook Flusso di lavoro PowerShell, ma con alcune differenze e limitazioni:

1.	L'esecuzione dei runbook PowerShell è più rapida rispetto ai runbook Flusso di lavoro PowerShell, perché non prevedono un passaggio di compilazione.
2.	Grazie al supporto dei checkpoint, i runbook Flusso di lavoro PowerShell possono riprendere da qualsiasi punto del runbook, mentre i runbook PowerShell possono riprendere solo dall'inizio.
3.	I runbook Flusso di lavoro PowerShell supportano l'esecuzione seriale e parallela, mentre i runbook PowerShell possono eseguire i comandi solo in modo seriale.
4.	In un runbook Flusso di lavoro PowerShell un'attività, un comando o un blocco di script può avere un proprio spazio di esecuzione, mentre in un runbook PowerShell tutti gli elementi in uno script vengono eseguiti in un singolo spazio di esecuzione. Esistono anche [differenze di sintassi](https://technet.microsoft.com/magazine/dn151046.aspx) tra un runbook PowerShell nativo e un runbook Flusso di lavoro PowerShell.

## Passaggi successivi

-	Per iniziare a usare runbook grafici, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md).
-	Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
-	Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
-	Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [supporto di script PowerShell nativi in Automazione di Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

<!---HONumber=AcomDC_0713_2016-->