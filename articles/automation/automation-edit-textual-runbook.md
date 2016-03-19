<properties 
	pageTitle="Modifica di runbook testuali in Automazione di Azure"
	description="In questo articolo vengono fornite procedure diverse per l'uso di PowerShell e dei runbook del flusso di lavoro PowerShell in Automazione di Azure mediante l'editor di testo."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="02/23/2016"
	ms.author="magoedte;bwren" />

# Modifica di runbook testuali in Automazione di Azure

L'editor di testo in Automazione di Azure può essere usato per modificare i [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Questo editor dispone delle funzionalità tipiche degli altri editor di codice, ad esempio IntelliSense e la codifica a colori con particolari funzionalità aggiuntive per semplificare l'accesso alle risorse comuni dei runbook. In questo articolo vengono fornite procedure dettagliate per l'esecuzione delle diverse funzioni disponibili in questo editor.

L'editor di testo include una funzionalità per inserire codice per attività, risorse e runbook figlio all'interno di un runbook. Invece di digitare il codice manualmente, è possibile effettuare selezioni in un elenco di risorse disponibili e inserire automaticamente il codice nel runbook.

Ogni runbook in Automazione di Azure include due versioni, ovvero una versione bozza e una versione pubblicata. Si modifica la versione bozza del runbook e quindi lo si pubblica in modo da poterlo eseguire. La versione pubblicata non può essere modificata. Per altre informazioni, vedere [Pubblicazione di un runbook](automation-creating-importing-runbook.md#publishing-a-runbook).

Per usare i [runbook grafici](automation-runbook-types.md#graphical-runbooks), vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

## Per modificare un runbook con il portale di Azure

Usare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nel portale di Azure selezionare l'account di automazione.
2. Fare clic nel pannello **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul nome del runbook che si desidera modificare e quindi fare clic sul pulsante **Modifica**.
6. Apportare le modifiche necessarie.
7. Fare clic su **Salva** dopo aver completato le modifiche desiderate.
8. Fare clic su **Pubblica** se si desidera pubblicare la versione bozza più recente del runbook.

### Per inserire un cmdlet in un runbook

2. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il cmdlet.
3. Espandere il nodo **Cmdlet** nel controllo della libreria. 
3. Espandere il modulo contenente il cmdlet che si desidera usare.
4. Fare clic con il pulsante destro sul cmdlet da inserire e selezionare **Aggiungi a canvas**. Se il cmdlet include più di un set di parametri, verrà aggiunto il set predefinito. È inoltre possibile espandere il cmdlet per selezionare un set di parametri diverso.
4. Il codice per il cmdlet viene inserito con l'intero elenco di parametri.
5. Fornire un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <> per gli eventuali parametri obbligatori. Rimuovere i parametri non necessari.

### Per inserire il codice per un runbook figlio all'interno di un runbook

2. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il [runbook figlio](automation-child-runbooks.md).
3. Espandere il nodo **Runbook** nel controllo della libreria. 
3. Fare clic con il pulsante destro sul runbook da inserire e selezionare **Aggiungi a canvas**.
4. Il codice relativo al runbook figlio viene inserito con qualsiasi segnaposto necessario per i parametri del runbook.
5. Sostituire i segnaposto con i valori appropriati per ogni parametro.

### Per inserire un asset in un runbook

2. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il runbook figlio.
3. Espandere il nodo **Asset** nel controllo della libreria. 
4. Espandere il nodo per il tipo di asset desiderato.
3. Fare clic con il pulsante destro sull'asset da inserire e selezionare **Aggiungi a canvas**. Per gli [asset di tipo variabile](automation-variables.md), selezionare l'opzione **Aggiungi "Ottieni variabile" a canvas** o **Aggiungi "Imposta variabile" a canvas** a seconda che si desideri ottenere o impostare la variabile.
4. Il codice per l'asset viene inserito nel runbook.



## Per modificare un runbook con il portale di Azure

Usare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nel portale di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
2. Fare clic sulla scheda **Runbook**.
3. Fare clic sul nome del runbook che si desidera modificare e quindi selezionare la scheda **Creazione**.
5. Fare clic sul pulsante **Modifica** nella parte inferiore della schermata.
6. Apportare le modifiche necessarie.
7. Fare clic su **Salva** dopo aver completato le modifiche desiderate.
8. Fare clic su **Pubblica** se si desidera pubblicare la versione bozza più recente del runbook.

### Per inserire un'attività in un runbook

1. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire l'attività.
1. Nella parte inferiore della schermata fare clic su **Inserisci** e quindi su **Attività**.
1. Nella colonna **Modulo di integrazione** selezionare il modulo che contiene l'attività.
1. Nel riquadro **Attività** selezionare un'attività.
1. Nella colonna **Descrizione** annotare la descrizione dell'attività. Facoltativamente, è possibile fare clic su Visualizza Guida dettagliata per avviare la Guida relativa all'attività nel browser.
1. Fare clic sulla freccia destra. Se l'attività dispone di parametri, questi verranno elencati come riferimento.
1. Fare quindi clic sul pulsante con il segno di spunta. Il codice per l'esecuzione dell'attività verrà inserito nel runbook.
1. Se l'attività richiede parametri, fornire un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <>.

### Per inserire il codice per un runbook figlio all'interno di un runbook

1. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il [runbook figlio](automation-child-runbooks.md).
2. Nella parte inferiore della schermata fare clic su **Inserisci** e quindi su **Runbook**.
3. Selezionare il runbook da inserire nella colonna centrale e fare clic sulla freccia a destra.
4. Se il runbook dispone di parametri, questi verranno elencati come riferimento.
5. Fare quindi clic sul pulsante con il segno di spunta. Il codice per l'esecuzione del runbook selezionato verrà inserito nel runbook corrente.
7. Se il runbook richiede parametri, fornire un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <>.

### Per inserire un asset in un runbook

1. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire l'attività per recuperare l'asset.
1. Nella parte inferiore della schermata fare clic su **Inserisci** e quindi su **Impostazione**.
1. Nella colonna **Azione impostazione** selezionare l'azione desiderata.
1. Effettuare una selezione tra gli asset disponibili nella colonna centrale.
1. Fare quindi clic sul pulsante con il segno di spunta. Il codice per ottenere o impostare l'asset verrà inserito nel runbook.



## Per modificare un runbook di Automazione di Azure tramite Windows PowerShell

Per modificare un runbook con Windows PowerShell, usare l'editor desiderato e salvarlo in un file con estensione PS1. È possibile usare il cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) per recuperare il contenuto del runbook e quindi il cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) per sostituire la versione bozza del runbook esistente con quello modificato.

### Per recuperare il contenuto di un runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come recuperare lo script per un runbook e salvarlo in un file di script. In questo esempio viene recuperata la versione bozza. È inoltre possibile recuperare la versione pubblicata del runbook, sebbene questa versione non possa essere modificata.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### Per modificare il contenuto di un Runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come sostituire il contenuto di un runbook con il contenuto di un file di script. Si noti che questa è la stessa procedura di esempio descritta in [Per importare un runbook da un file di script con Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## Articoli correlati

- [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)
- [Informazioni sul flusso di lavoro PowerShell](automation-powershell-workflow.md)
- [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
- [Certificati](automation-certificates.md)
- [Connessioni](automation-connections.md)
- [Credenziali](automation-credentials.md)
- [Pianificazioni](automation-schedules.md)
- [Variabili](automation-variables.md)

<!---HONumber=AcomDC_0302_2016-->