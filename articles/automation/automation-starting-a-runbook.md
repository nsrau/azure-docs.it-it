<properties
   pageTitle="Avvio di un runbook in Automazione di Azure | Microsoft Azure"
   description="Riepiloga le diverse modalità che è possibile usare per avviare un Runbook in Automazione di Azure e fornisce dettagli sull'uso del portale di Azure e di Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="magoedte;bwren"/>

# Avvio di un Runbook in Automazione di Azure

La tabella seguente consente di determinare la modalità di avvio di un Runbook in Automazione di Azure più appropriata per il proprio scenario. Questo articolo include informazioni dettagliate sull'avvio di un Runbook con il portale di Azure e con Windows PowerShell. Le informazioni sulle altre modalità sono contenute in altri documenti accessibili dai collegamenti seguenti.

| **METODO** | **CARATTERISTICHE** |
|-------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Portale di Azure](#starting-a-runbook-with-the-azure-portal) | <li>Metodo più semplice con interfaccia utente interattiva.<br> <li>Modulo per specificare valori di parametri semplici.<br> <li>Facilità di controllo dello stato dei processi.<br> <li>Accesso autenticato con l'accesso di Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) | <li>Chiamata mediante cmdlet di Windows PowerShell nella riga di comandi.<br> <li>Possibilità di inclusione in una soluzione automatizzata con più passaggi.<br> <li>Autenticazione della richiesta con un certificato oppure con un'entità utente/entità servizio OAuth.<br> <li>Possibilità di specificare valori di parametri semplici e complessi.<br> <li>Possibilità di controllare lo stato dei processi.<br> <li>Obbligo per il client di supporto dei cmdlet di PowerShell. |
| [API di Automazione di Azure](http://msdn.microsoft.com/library/azure/mt163849.aspx) | <li>Modalità più flessibile, ma anche più complessa.<br> <li>Possibilità di chiamata da qualsiasi codice personalizzato in grado di creare richieste HTTP.<br> <li>Autenticazione della richiesta con un certificato oppure con un'entità utente/entità servizio OAuth.<br> <li>Possibilità di specificare valori di parametri semplici e complessi.<br> <li>Possibilità di controllare lo stato dei processi. |
| [Webhook](automation-webhooks.md) | <li>Avvio di Runbook da una singola richiesta HTTP.<br> <li>Autenticazione con token di sicurezza nell'URL.<br> <li>Impossibilità per il client di eseguire l'override dei valori di parametri specificati al momento della creazione del webhook. Possibilità per il Runbook di definire un singolo parametro popolato con i dettagli della richiesta HTTP.<br> <li>Nessuna possibilità di tenere traccia dello stato dei processi tramite URL webhook. |
| [Risposta all'avviso di Azure](automation-webhooks.md) | <li>Avviare un runbook in risposta all'avviso di Azure.<br> <li>Configurare webhook per runbook e collegare all'avviso.<br> <li>Autenticazione con token di sicurezza nell'URL.<br> <li>Attualmente supporta solo per avvisi sulle metriche. |
| [Pianificazione](automation-scheduling-a-runbook.md) | <li>Avvio automatico dei Runbook in base a una pianificazione oraria, giornaliera o settimanale.<br> <li>Modifica della pianificazione tramite il portale di Azure, i cmdlet di PowerShell o l'API di Azure.<br> <li>Possibilità di specificare i valori di parametri da usare con la pianificazione. |
| [Da un altro Runbook](automation-child-runbooks.md) | <li>Uso di un Runbook come attività in un altro Runbook.<br> <li>Utile per funzionalità usate da più Runbook.<br> <li>Possibilità di specificare valori di parametri per Runbook figlio e usare l'output in Runbook padre. |

L'immagine seguente illustra in dettaglio il processo del ciclo di vita di un runbook che include diverse modalità di avvio di un runbook in Automazione di Azure, i componenti necessari in un computer locale per eseguire i runbook di Automazione di Azure e le interazioni tra diversi componenti. Per altre informazioni sull'esecuzione di runbook di automazione nel proprio data center, vedere [Ruoli di lavoro ibridi per runbook](automation-hybrid-runbook-worker.md)

![Architettura dei runbook](media/automation-starting-runbook/runbooks-architecture.png)

## Avvio di un Runbook con il portale di Azure

1.	Nel portale di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
2.	Fare clic sulla scheda **Runbook**.
3.	Selezionare un Runbook e quindi fare clic su **Avvia**.
4.	Se il Runbook dispone di parametri, verrà richiesto di specificare i valori con una casella di testo per ogni parametro. Per altri dettagli sui parametri, vedere [Parametri di Runbook](#Runbook-parameters) più avanti.
5.	Selezionare **Visualizza processo** accanto al messaggio del Runbook **Avvio in corso** oppure selezionare la scheda **Processi** per consentire al Runbook di visualizzare lo stato del processo.

## Avvio di un Runbook con il portale di Azure

1.	Dall'account di automazione fare clic sulla parte **Runbook** per aprire il pannello **Runbook**.
2.	Fare clic su un Runbook per aprire il pannello **Runbook** corrispondente.
3.	Fare clic su **Avvia**.
4.	Se il Runbook non dispone di alcun parametro, verrà richiesto di confermare se si desidera avviarlo. Se invece il Runbook dispone di parametri, verrà visualizzato il pannello **Avvia Runbook**, in cui sarà possibile specificare i valori di parametri. Per altri dettagli sui parametri, vedere [Parametri di Runbook](#Runbook-parameters) più avanti.
5.	Verrà visualizzato il pannello **Processo**, in cui è possibile tenere traccia dello stato del processo.

## Avvio di un Runbook con Windows PowerShell

È possibile usare [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) per avviare un Runbook con Windows PowerShell. Il codice di esempio seguente avvia un Runbook denominato Test-Runbook.

```
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
```

Start-AzureAutomationRunbook restituisce un oggetto processo che è possibile usare per tenere traccia dello stato dopo l'avvio del Runbook. È quindi possibile usare questo oggetto processo con [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) per determinare lo stato del processo e con [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) per ottenere il relativo output. L'esempio di codice seguente avvia un Runbook denominato Test-Runbook, attende che venga completato e quindi visualizza l'output corrispondente.

```
$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped")
}

Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output
```

Se il Runbook richiede parametri, è necessario fornirli come [tabella hash](http://technet.microsoft.com/library/hh847780.aspx), dove la chiave della tabella hash corrisponde al nome del parametro e il valore al valore del parametro. L'esempio seguente illustra come avviare un Runbook con due parametri di stringa denominati FirstName e LastName, un parametro di tipo intero denominato RepeatCount e un parametro booleano denominato Show. Per altre informazioni sui parametri, vedere [Parametri di Runbook](#Runbook-parameters) più avanti.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params
```

## Parametri di Runbook

Quando si avvia un Runbook mediante il portale di gestione di Azure o Windows PowerShell, l'istruzione viene inviata attraverso il servizio Web Automazione di Azure. Questo servizio non supporta i parametri con tipi di dati complessi. Se è necessario specificare un valore per un parametro complesso, eseguire la chiamata inline da un altro Runbook come descritto in [Runbook figlio in Automazione di Azure](automation-child-runbooks.md).

Il servizio Web Automazione di Azure offrirà funzionalità speciali per i parametri che usano determinati tipi di dati, come descritto nelle sezioni seguenti.

### Valori denominati

Se il parametro è un tipo di dati [object], è possibile usare il formato JSON seguente per inviargli un elenco di valori denominati: *{"Name1":Value1, "Name2":Value2, "Name3":Value3}*. Questi valori devono essere tipi semplici. Il Runbook riceverà il parametro come [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) con proprietà che corrispondono a ogni valore denominato.

Si consideri il Runbook di test seguente che accetta un parametro denominato user.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    if ($user.Show) {
        foreach ($i in 1..$user.RepeatCount) {
            $user.FirstName
            $user.LastName
        }
    }
}
```

È possibile usare il testo seguente per il parametro user.

```
{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}
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

-	L'architettura di runbook nell'articolo corrente fornisce una descrizione generale dei runbook ibridi. Per altre informazioni, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md)

<!---HONumber=AcomDC_0224_2016-->