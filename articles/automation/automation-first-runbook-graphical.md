<properties
	pageTitle="Il primo runbook grafico in Automazione di Azure | Microsoft Azure"
	description="Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook grafico ."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="09/17/2015"
	ms.author="bwren"/>


# Il primo runbook grafico

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [PowerShell Workflow](automation-first-runbook-textual.md)
- [PowerShell](automation-first-runbook-textual-PowerShell.md)

Questa esercitazione illustra la creazione di un [runbook grafico](automation-runbook-types.md#graphical-runbooks) in Automazione di Azure. Si inizierà con un runbook semplice che sarà testato e pubblicato, quindi verrà illustrato come tenere traccia dello stato del processo del runbook. Si modificherà quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure. Si renderà quindi il runbook più affidabile aggiungendo i relativi parametri e un collegamento condizionale.

## Prerequisiti

Per completare questa esercitazione, sono necessari gli elementi seguenti.

- sottoscrizione Azure. Se non si dispone ancora di una sottoscrizione, è possibile [attivare i benefici per i sottoscrittori MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure <a href="/pricing/free-trial/" target="_blank">[iscriversi per ottenere una versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
- [Account di automazione](automation-configuring.md) che conterrà il runbook.
- Macchina virtuale di Azure. Si arresterà e si avvierà la macchina virtuale in modo che non sia di produzione.
- [Utente di Azure Active Directory e Asset credenziali di Automazione](automation-configuring.md) per l'autenticazione con le risorse di Azure. Questo utente deve avere l'autorizzazione per arrestare e avviare la macchina virtuale.

## Passaggio 1: Creare nuovo runbook

Si inizierà creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di anteprima di Azure aprire l'account di automazione. La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di automazione. Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.<br> ![Controllo Runbook](media/automation-first-runbook-graphical/runbooks-control.png)
2. Creare un nuovo runbook facendo clic sul pulsante **Aggiungi Runbook** e quindi su **Crea un nuovo runbook**.
3. Denominare il runbook *MyFirstRunbook-Graphical*.
4. In questo caso, si creerà un[runbook grafico](automation-graphical-authoring-intro.md), quindi selezionare **Grafico** come **Tipo di Runbook**.<br>![Nuovo runbook](media/automation-first-runbook-graphical/new-runbook.png)
5. Fare clic su **Crea** per creare il runbook e aprire l'editor grafico.

## Passaggio 2: Aggiungere attività al runbook

Il controllo Libreria sul lato sinistro dell'editor consente di aggiungere attività al runbook. Si aggiungerà un cmdlet **Write-Output** per l'output del testo da parte del runbook.

1.   Nel controllo Libreria espandere il nodo **Cmdlet** e quindi **Microsoft.PowerShell.Utility**.<br>![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.   Scorrere fino alla fine dell'elenco. Fare clic con il pulsante destro del mouse su **Write-Output** e scegliere **Aggiungi a area di disegno**.
4.   Fare clic sull'attività **Write-Output** nell'area di disegno. Viene aperto il controllo Configurazione che consente di configurare l'attività.
5.   In **Etichetta** è impostato il nome predefinito del cmdlet, ma è possibile modificarlo specificando un nome più descrittivo. Cambiarlo in *Scrivere Hello World nell'output*.
6.   Fare clic su **Parametri** per specificare i valori per i parametri del runbook. Alcuni cmdlet hanno più set di parametri ed è necessario selezionare quale sarà usato. In questo caso, **Write-Output** ha un solo set di parametri, quindi non è necessario selezionarne uno.<br>![Proprietà Write-Output](media/automation-first-runbook-graphical/write-output-properties.png)
7.   Selezionare il parametro **InputObject**. Questo è il parametro in cui si specificherà il testo da inviare al flusso di output.
9.   Nell'elenco a discesa **Origine dati** selezionare **Espressione PowerShell**. L'elenco a discesa **Origine dati** include diverse origini che è possibile usare per popolare il valore di un parametro. È possibile usare l'output da tali origini, ad esempio un'altra attività, un asset di automazione o un'espressione di PowerShell. In questo caso, si vuole solo generare il testo *Hello World*. È possibile usare un'espressione di PowerShell e specificare una stringa.
10.   Nella casella **Espressione** digitare *"Hello World"* e quindi fare clic su **OK** due volte per tornare all'area di disegno.<br> ![PowerShell Expression](media/automation-first-runbook-graphical/expression-hello-world.png)
11.   Salvare il runbook facendo clic su **Salva**.<br> ![Salvataggio del runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.
 
2. Fare clic su **Pannello di test** per aprire il pannello di test.<br>![Riquadro Test](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
3. Viene creato un [processo del runbook](automation-runbook-execution) e il relativo stato viene visualizzato nel riquadro. Lo stato del processo verrà avviato come *In coda* per indicare che è in attesa della disponibilità di un thread di lavoro del runbook nel cloud. Lo stato passerà quindi a *Avvio in corso* quando un thread di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
4. Al termine del processo del runbook, viene visualizzato l'output. In questo caso, dovrebbe essere visualizzato *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5. Chiudere il riquadro di test per tornare all'area di disegno.


## Passaggio 4: Pubblicare e avviare il runbook

Il runbook appena creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su**Pubblica** per pubblicare il runbook, quindi **Sì** quando richiesto.<br> ![Publish](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2. Se ora si scorre verso sinistra per visualizzare il runbook nel pannello **Runbook**, come **Stato di creazione** viene visualizzato**Pubblicato**.
3. Scorrere verso destra per visualizzare il riquadro **MyFirstRunbook**. Le opzioni nella parte superiore consentono di avviare il runbook, pianificarlo per avviarlo in qualsiasi momento in futuro o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP. 
4. Per avviare semplicemente il runbook, fare clic su **Avvia** e quindi su **Sì** quando richiesto.<br>![Avvia runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5. Viene aperto un riquadro del processo per il processo del runbook appena creato. È possibile chiudere questo riquadro, ma in questo caso lo si lascerà aperto per poter controllare l'avanzamento del processo.
6.  Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.<br> ![Riepilogo dei processi](media/automation-first-runbook-graphical/job-pane-summary.png)
7.  Quando lo stato del runbook risulta *Completato* fare clic su **Output**. Viene aperto il riquadro **Output** dove si può vedere il testo *Hello World*.<br> ![Riepilogo dei processi](media/automation-first-runbook-graphical/job-pane-output.png)  
8.  Chiudere il riquadro Output.
9.  Fare clic su **Flussi** per aprire il riquadro dei flussi per il processo del runbook. Nel flusso di output dovrebbe essere visibile solo *Hello World*, ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi.<br> ![Riepilogo dei processi](media/automation-first-runbook-graphical/job-pane-streams.png) 
9. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare al riquadro MyFirstRunbook.
9.  Fare clic su**Processi** per aprire il pannello dei processi per questo runbook. Sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.<br> ![Processi](media/automation-first-runbook-graphical/runbook-control-jobs.png) 
9. È possibile fare clic su questo processo per aprire lo stesso riquadro del processo visualizzato quando è stato avviato il runbook. In questo modo è possibile tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## Passaggio 5: Aggiungere l'autenticazione per gestire le risorse di Azure

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure. Sarà tuttavia in grado di eseguire questa operazione solo dopo aver fatto in modo che esegua l'autenticazione con le credenziali indicate nei [prerequisiti](#prerequisites). A questo scopo si userà il cmdlet **Set-AzureAccount**.

1.  Aprire l'editor grafico facendo clic su **Modifica** nel riquadro MyFirstRunbook<br> ![Modificare il runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png) 
2.  **Scrivere Hello World nell'output** non è più necessario, quindi fare clic con il pulsante destro del mouse sul testo e scegliere **Elimina**.
8.  Nel controllo Libreria, espandere **Cmdlet** e poi **Azure**. 
9.  Aggiungere **Add-AzureAccount** all'area di disegno.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png) 
12.  Selezionare **Add-AzureAccount** e poi fare clic su **Parametri** nel pannello di configurazione.
13.  **Add-AzureAccount** include più set di parametri, quindi è necessario selezionarne uno prima di poter fornire i valori dei parametri. Fare clic su **Set di parametri** e poi selezionare il set di parametri **Utente**.
14.  Dopo aver selezionato il set di parametri, nel riquadro Configurazione parametro attività vengono visualizzati i parametri. Fare clic su **Credenziale**.<br> ![Aggiungere parametri all'account Azure](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
15.  Per fare in modo che questo cmdlet usi un asset credenziali nell'account di automazione, selezionare **Asset credenziali** come **Origine dati**.
16.  Selezionare l'asset credenziali con l'autorizzazione di accesso per avviare e arrestare una macchina virtuale nell'ambiente Azure.<br> ![Aggiungere l'origine dati all'account Azure](media/automation-first-runbook-graphical/credential-data-source.png)
17.  Fare clic su **OK** due volte per tornare all'area di disegno.


## Passaggio 6: Aggiungere l'attività per avviare una macchina virtuale

Ora si aggiungerà un'attività **Start-AzureVM** per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome verrà hardcoded nel cmdlet.

1. Espandere **Cmdlet** e quindi **Azure**.
2. Aggiungere **Start-AzureVM** all'area di disegno e quindi fare clic e trascinarla sotto **Add-AzureAccount**.
11.  Passare il mouse su **Add-AzureAccount** finché nella parte inferiore della forma non compare un cerchio. Fare clic sul cerchio e trascinare la freccia su **Start-AzureVM**. La freccia appena creata è un *collegamento*. Il runbook verrà avviato con **Add-AzureAccount** e poi eseguirà **Start-AzureVM**.<br>![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
5. Selezionare **Start-AzureVM**. Fare clic su **Parametri** e poi su **Set di parametri**per visualizzare i set per **Start-AzureVM**. Selezionare il set di parametri **ByName**. Si noti che accanto a **Name** e **ServiceName** sono presenti punti esclamativi. Indicano che si tratta di parametri obbligatori.
7. Selezionare **Name**. Usare **Valore costante** per **Origine dati** e digitare il nome della macchina virtuale da avviare. Fare clic su **OK**.
8. Selezionare **ServiceName**. Usare **Valore costante** per **Origine dati** e digitare il nome della macchina virtuale da avviare. Fare clic su **OK**.<br> ![Parametri Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params.png)
9. Fare clic sul riquadro Test in modo da testare il runbook.
10. Fare clic su **Avvia** per avviare il test. Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.


## Passaggio 7: Aggiungere un parametro di input al runbook

Ora il runbook avvia la macchina virtuale specificata nel cmdlet **Start-AzureVM**, ma il runbook sarebbe più utile se si potesse specificare la macchina virtuale quando esso si avvia. Per fornire questa funzionalità, ora si aggiungerà un parametro di input al runbook.

1. Aprire l'editor grafico facendo clic su **Modifica** nel pannello **MyFirstRunbook**.
2. Fare clic su **Input e output** e quindi**Aggiungi input** per aprire il riquadro Parametro di input runbook.<br>![Input e output del runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
4. Specificare *VMName* per **Nome**. Mantenere *stringa* come **Tipo**, ma cambiare **Obbligatorio** in *Sì*. Fare clic su **OK**.
5. Creare un secondo parametro di input obbligatorio chiamato *VMServiceName* e poi fare clic su **OK**per chiudere il pannello ** Input e output**.<br>![Parametri di input del runbook](media/automation-first-runbook-graphical/input-parameters.png) 
6. Selezionare l'attività **Start-AzureVM** e poi fare clic su **Parametri**.
7. Cambiare **Origine dati** per **Nome** in **Input runbook** e poi selezionare **VMName**.<br> ![Parametro Name di Start-AzureVM](media/automation-first-runbook-graphical/vmname-property.png) 
8. Cambiare **Origine dati** per **ServiceName** in **Input runbook** e poi selezionare **VMServiceName**.<br>![Parametri Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png) 
9. Salvare il runbook e aprire il riquadro Test. Si noti che ora è possibile fornire i valori per le due variabili di input che verranno usate nel test. 
11.  Chiudere il riquadro Test.
12.  Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
13.  Arrestare la macchina virtuale avviata nel passaggio precedente.
13.  Fare clic su **Avvia** per avviare il runbook. Digitare **VMName** e **VMServiceName** per la macchina virtuale da avviare.<br> ![Avviare il Runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png) 
14.  Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## Passaggio 8: Creare un collegamento condizionale

Ora si modificherà il runbook in modo che provi solo ad avviarsi, se non è già avviato. Per eseguire questa operazione, aggiungere un cmdlet **Get-AzureVM** al runbook che includerà lo stato corrente della macchina virtuale. Si aggiungerà quindi un collegamento condizionale che eseguirà **Start-AzureVM** solo se lo stato di esecuzione corrente è arrestato. Se il runbook non è arrestato, verrà generato un messaggio.

1. Aprire **MyFirstRunbook** nell'editor grafico.
2. Rimuovere il collegamento tra **Add-AzureAccount** e **Start-AzureVM** facendo clic su di esso e quindi premendo*CANC*.
3. Nel controllo Libreria espandere **Cmdlet** e poi **Azure**.
4. Aggiungere **Get-AzureVM** all'area di disegno.
5. Creare un collegamento da **Add-AzureAccount** a **Get-AzureVM**. Creare un altro collegamento da **Get-AzureVM** a **Start-AzureVM**.<br> ![Runbook con Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)   
6.  Selezionare **Get-AzureVM** e clic su **Parametri**. Selezionare il set di parametri *GetVMByServiceAndVMName*.
7.  Per **Nome** impostare **Origine dati** su **Input runbook** e poi selezionare **VMName**.   
7.  Per **ServiceName** impostare **Origine dati** su **Input runbook** e quindi selezionare **VMServiceName**.  
8.  Selezionare il collegamento tra **Get-AzureVM** e **Start-AzureVM**.
9.  Nel riquadro di configurazione cambiare **Applica condizione** in **True**. Si noti che il collegamento si trasforma in una linea tratteggiata per indicare che l'attività di destinazione verrà eseguita solo se la condizione restituisce true.
10.  Per **Espressione condizione** digitare *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*. Ora **Start-AzureVM** verrà eseguita solo se la macchina virtuale è arrestata.<br> ![Condizione del collegamento](media/automation-first-runbook-graphical/link-condition.png) 
11.  Nel controllo Libreria, espandere **Cmdlet** e poi **Microsoft.PowerShell.Utility**.
12.  Aggiungere **Write-Output** all'area di disegno.
13.  Creare un collegamento da **Get-AzureVM** a **Write-Output**.
14.  Selezionare il collegamento e cambiare **Applica condizione** in **True**.
14.  Per **Espressione condizione** digitare *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*. Ora **Write-Output** verrà eseguita solo se la macchina virtuale non è arrestata.<br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/write-output-link.png) 
15.  Selezionare **Write-Output** e fare clic su **Parametri**.
16.  Per **InputObject** cambiare **Origine dati** in **Espressione PowerShell** e digitare l'espressione *"$VMName.Name already started."*.
17.  Salvare il runbook e aprire il riquadro Test.
18.  Avviare il runbook con la macchina virtuale arrestata, che dovrebbe essere avviata.
19.  Avviare il runbook con la macchina virtuale avviata. L'output dovrebbe indicare che è già in avviata.
 

## Articoli correlati

- [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
- [Il primo runbook di flusso di lavoro PowerShell](automation-first-runbook-textual.md)
- [Il primo runbook PowerShell](automation-first-runbook-textual-PowerShell.md)
 

<!---HONumber=AcomDC_0107_2016-->