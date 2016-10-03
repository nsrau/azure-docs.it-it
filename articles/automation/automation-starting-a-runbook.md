<properties
   pageTitle="Avvio di un runbook in Automazione di Azure | Microsoft Azure"
   description="Riepiloga le diverse modalità che è possibile usare per avviare un Runbook in Automazione di Azure e fornisce dettagli sull'uso del portale di Azure e di Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />  
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="magoedte;bwren"/>  

# Avvio di un Runbook in Automazione di Azure

La tabella seguente consente di determinare la modalità di avvio di un Runbook in Automazione di Azure più appropriata per il proprio scenario. Questo articolo include informazioni dettagliate sull'avvio di un Runbook con il portale di Azure e con Windows PowerShell. Le informazioni sulle altre modalità sono contenute in altri documenti accessibili dai collegamenti seguenti.

| **METODO** | **CARATTERISTICHE** |
|-------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Portale di Azure](#starting-a-runbook-with-the-azure-portal) | <li>Metodo più semplice con interfaccia utente interattiva.<br> <li>Modulo per specificare valori di parametri semplici.<br> <li>Facilità di controllo dello stato dei processi.<br> <li>Accesso autenticato con l'accesso di Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) | <li>Chiamata mediante cmdlet di Windows PowerShell nella riga di comando.<br> <li>Possibilità di inclusione in una soluzione automatizzata con più passaggi.<br> <li>Autenticazione della richiesta con un certificato oppure con un'entità utente/entità servizio OAuth.<br> <li>Possibilità di specificare valori di parametri semplici e complessi.<br> <li>Possibilità di controllare lo stato dei processi.<br> <li>Obbligo per il client di supporto dei cmdlet di PowerShell. |
| [API di Automazione di Azure](https://msdn.microsoft.com/library/azure/mt662285.aspx) | <li>Modalità più flessibile, ma anche più complessa.<br> <li>Possibilità di chiamata da qualsiasi codice personalizzato in grado di creare richieste HTTP.<br> <li>Autenticazione della richiesta con un certificato oppure con un'entità utente/entità servizio OAuth.<br> <li>Possibilità di specificare valori di parametri semplici e complessi.<br> <li>Possibilità di controllare lo stato dei processi. |
| [Webhook](automation-webhooks.md) | <li>Avvio di Runbook da una singola richiesta HTTP.<br> <li>Autenticazione con token di sicurezza nell'URL.<br> <li>Impossibilità per il client di eseguire l'override dei valori dei parametri specificati al momento della creazione del webhook. Possibilità per il Runbook di definire un singolo parametro popolato con i dettagli della richiesta HTTP.<br> <li>Impossibilità di tenere traccia dello stato dei processi tramite URL webhook. |
| [Risposta all'avviso di Azure](../log-analytics/log-analytics-alerts.md) | <li>Avviare un runbook in risposta all'avviso di Azure.<br> <li>Configurare webhook per runbook e collegare all'avviso.<br> <li>Autenticazione con token di sicurezza nell'URL.<br> <li>Attualmente supporta solo per avvisi sulle metriche. |
| [Pianificazione](automation-scheduling-a-runbook.md) | <li>Avvio automatico dei Runbook in base a una pianificazione oraria, giornaliera o settimanale.<br> <li>Modifica della pianificazione tramite il portale di Azure, i cmdlet di PowerShell o l'API di Azure.<br> <li>Possibilità di specificare i valori di parametri da usare con la pianificazione. |
| [Da un altro Runbook](automation-child-runbooks.md) | <li>Uso di un Runbook come attività in un altro Runbook.<br> <li>Utile per funzionalità usate da più Runbook.<br> <li>Possibilità di specificare valori di parametri per Runbook figlio e usare l'output in Runbook padre. |

L'immagine seguente illustra in dettaglio il processo nel ciclo di vita di un runbook. Include vari metodi di avvio di un runbook in Automazione di Azure, i componenti necessari per il ruolo di lavoro ibrido per runbook per eseguire i runbook di Automazione di Azure e le interazioni tra i vari componenti. Per altre informazioni sull'esecuzione di runbook di automazione nel proprio data center, vedere [Ruoli di lavoro ibridi per runbook](automation-hybrid-runbook-worker.md)

![Architettura dei runbook](media/automation-starting-runbook/runbooks-architecture.png)  

## Avvio di un Runbook con il portale di Azure

1.	Dall'account di automazione fare clic sulla parte **Runbook** per aprire il pannello **Runbook**.
2.	Fare clic su un Runbook per aprire il pannello **Runbook** corrispondente.
3.	Fare clic su **Avvia**.
4.	Se il Runbook non dispone di alcun parametro, verrà richiesto di confermare se si desidera avviarlo. Se invece il Runbook dispone di parametri, verrà visualizzato il pannello **Avvia Runbook**, in cui sarà possibile specificare i valori di parametri. Per altri dettagli sui parametri, vedere [Parametri di Runbook](#Runbook-parameters) più avanti.
5.	Verrà visualizzato il pannello **Processo**, in cui è possibile tenere traccia dello stato del processo.

## Avvio di un Runbook con Windows PowerShell

È possibile usare [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) per avviare un runbook con Windows PowerShell. Il codice di esempio seguente avvia un Runbook denominato Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook restituisce un oggetto processo che è possibile usare per tenere traccia dello stato dopo l'avvio del runbook. È quindi possibile usare questo oggetto processo con [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) per determinare lo stato del processo e con [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) per ottenere il relativo output. L'esempio di codice seguente avvia un Runbook denominato Test-Runbook, attende che venga completato e quindi visualizza l'output corrispondente.

```
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Se il Runbook richiede parametri, è necessario fornirli come [tabella hash](http://technet.microsoft.com/library/hh847780.aspx), dove la chiave della tabella hash corrisponde al nome del parametro e il valore al valore del parametro. L'esempio seguente illustra come avviare un Runbook con due parametri di stringa denominati FirstName e LastName, un parametro di tipo intero denominato RepeatCount e un parametro booleano denominato Show. Per altre informazioni sui parametri, vedere [Parametri di Runbook](#Runbook-parameters) più avanti.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## Parametri di Runbook

Quando si avvia un runbook dal portale di Azure o da Windows PowerShell, l'istruzione viene inviata attraverso il servizio Web Automazione di Azure. Questo servizio non supporta i parametri con tipi di dati complessi. Se è necessario specificare un valore per un parametro complesso, eseguire la chiamata inline da un altro Runbook come descritto in [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).

Il servizio Web Automazione di Azure offrirà funzionalità speciali per i parametri che usano determinati tipi di dati, come descritto nelle sezioni seguenti.

### Valori denominati

Se il parametro è un tipo di dati [object], è possibile usare il formato JSON seguente per inviargli un elenco di valori denominati: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Questi valori devono essere tipi semplici. Il Runbook riceverà il parametro come [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) con proprietà che corrispondono a ogni valore denominato.

Si consideri il Runbook di test seguente che accetta un parametro denominato user.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
	if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

È possibile usare il testo seguente per il parametro user.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Si ottiene l'output seguente.

```
Joe
Smith
Joe
Smith
```

### Matrici

Se il parametro è una matrice, ad esempio [array] o [string], è possibile usare il formato JSON seguente per inviargli un elenco di valori: *[Value1,Value2,Value3]*. Questi valori devono essere tipi semplici.

Si consideri il Runbook di test seguente che accetta un parametro denominato *user*.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

È possibile usare il testo seguente per il parametro user.

```
["Joe","Smith",2,true]
```

Si ottiene l'output seguente.

```
Joe
Smith
Joe
Smith
```

### Credenziali

Se il parametro è un tipo di dati **PSCredential**, è possibile specificare il nome di un [asset credenziali](automation-credentials.md) di Automazione di Azure. Il Runbook recupererà le credenziali con il nome specificato.

Si consideri il Runbook di test seguente che accetta un parametro denominato credential.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

È possibile usare il testo seguente per il parametro user presumendo l'esistenza di un asset credenziali denominato *My Credential*.

```
My Credential
```

Presupponendo che il nome utente nelle credenziali sia *jsmith*, si ottiene l'output seguente.

```
jsmith
```

## Passaggi successivi

-	L'architettura runbook nell'articolo corrente offre una panoramica generale delle risorse di gestione di runbook in Azure e in locale con il ruolo di lavoro ibrido per runbook. Per altre informazioni sull'esecuzione di runbook di automazione nel proprio data center, vedere [Ruoli di lavoro ibridi per runbook](automation-hybrid-runbook-worker.md).
-	Per altre informazioni sulla creazione di runbook modulari che possono essere usati da altri runbook per funzioni comuni o specifiche, fare riferimento a [Runbook figlio](automation-child-runbooks.md).

<!---HONumber=AcomDC_0921_2016-->