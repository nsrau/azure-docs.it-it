<properties 
   pageTitle="Avvio di un runbook in Automazione di Azure"
   description="Riepiloga le diverse modalità che è possibile usare per avviare un runbook in Automazione di Azure e fornisce dettagli sull'uso del portale di Azure e di Windows PowerShell."
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
   ms.date="05/19/2015"
   ms.author="bwren" />

# Avvio di un runbook in Automazione di Azure

La tabella seguente consente di determinare la modalità di avvio di un runbook in Automazione di Azure più appropriata per il proprio scenario. Questo articolo include informazioni dettagliate sull'avvio di un runbook con il portale di Azure e con Windows PowerShell. Le informazioni sulle altre modalità sono contenute in altri documenti accessibili dai collegamenti seguenti.

<table>
 <tr>
  <td>METODO</td>
  <td>CARATTERISTICHE</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Portale di Azure</a></td>
  <td>
   <ul>
    <li>Metodo più semplice con interfaccia utente interattiva.</li>
    <li>Modulo per specificare valori di parametri semplici.</li>
    <li>Facilità di controllo dello stato dei processi.</li>
    <li>Accesso autenticato con l'accesso di Azure.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>Chiamata mediante cmdlet di Windows PowerShell nella riga di comandi.</li>
     <li>Possibilità di inclusione in una soluzione automatizzata con più passaggi.</li>
     <li>Autenticazione della richiesta con un certificato oppure con un'entità utente/entità servizio OAuth.</li>
     <li>Possibilità di specificare valori di parametri semplici e complessi.</li>
     <li>Possibilità di controllare lo stato dei processi.</li>
     <li>Obbligo per il client di supporto dei cmdlet di PowerShell.</li>
   </ul>
  </td>
 </tr>
 <tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">API di Automazione di Azure</a></td>
  <td>
   <ul>
    <li>Modalità più flessibile, ma anche più complessa.</li>
    <li>Possibilità di chiamata da qualsiasi codice personalizzato in grado di creare richieste HTTP.</li>
    <li>Autenticazione della richiesta con un certificato oppure con un'entità utente/entità servizio OAuth.</li>
    <li>Possibilità di specificare valori di parametri semplici e complessi.</li>
    <li>Possibilità di controllare lo stato dei processi.</li>
   </ul>
  </td>
 </tr>
 <tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>Avvio di runbook da una singola richiesta HTTP.</li>
    <li>Autenticazione con token di sicurezza nell'URL.</li>
    <li>Impossibilità per il client di eseguire l'override dei valori di parametri specificati al momento della creazione del webhook. Possibilità per il runbook di definire un singolo parametro popolato con i dettagli della richiesta HTTP.</li>
    <li>Nessuna possibilità di tenere traccia dello stato dei processi tramite URL webhook.</li>
   </ul>
  </td>
 </tr>
 <tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">Pianificazione</a></td>
  <td>
   <ul>
    <li>Avvio automatico dei runbook in base a una pianificazione oraria, giornaliera o settimanale.</li>
    <li>Modifica della pianificazione tramite il portale di Azure, i cmdlet di PowerShell o l'API di Azure.</li>
    <li>Possibilità di specificare i valori di parametri da usare con la pianificazione.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/dn857355.aspx">Da un altro runbook</a></td>
  <td>
   <ul>
    <li>Uso di un runbook come attività in un altro runbook.</li>
    <li>Utile per funzionalità usate da più runbook.</li>
    <li>Possibilità di specificare valori di parametri per runbook figlio e usare l'output in runbook padre.</li>
   </ul>
  </td>
 </tr>
</table>
<br>



## Avvio di un runbook con il portale di Azure

1. Nel portale di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
1. Fare clic sulla scheda **Runbook**.
1. Selezionare un runbook e quindi fare clic su **Avvia**.
1. Se il runbook dispone di parametri, verrà richiesto di specificare i valori con una casella di testo per ogni parametro. Per altri dettagli sui parametri, vedere [Parametri di runbook](#Runbook-parameters) più avanti.
1. Selezionare **Visualizza processo** accanto al messaggio del runbook **Avvio in corso** oppure selezionare la scheda **Processi** per consentire al runbook di visualizzare lo stato del processo.

## Avvio di un runbook con il portale di anteprima di Azure

1. Dall'account di automazione fare clic sulla parte **Runbook** per aprire il pannello **Runbook**.
1. Fare clic su un runbook per aprire il pannello **Runbook** corrispondente.
2. Fare clic su **Avvia**.
1. Se il runbook non dispone di alcun parametro, verrà richiesto di confermare se si desidera avviarlo. Se invece il runbook dispone di parametri, verrà visualizzato il pannello **Avvia runbook**, in cui sarà possibile specificare i valori di parametri. Per altri dettagli sui parametri, vedere [Parametri di runbook](#Runbook-parameters) più avanti.
3. Verrà visualizzato il pannello **Processo**, in cui è possibile tenere traccia dello stato del processo.


## Avvio di un runbook con Windows PowerShell

È possibile usare [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) per avviare un runbook con Windows PowerShell. Il codice di esempio seguente avvia un runbook denominato Test-Runbook.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook restituisce un oggetto processo che è possibile usare per tenere traccia dello stato dopo l'avvio del runbook. È quindi possibile usare questo oggetto processo con [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) per determinare lo stato del processo e con [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) per ottenere il relativo output. L'esempio di codice seguente avvia un runbook denominato Test-Runbook, attende che venga completato e quindi visualizza l'output corrispondente.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Se il runbook richiede parametri, è necessario fornirli come [tabella hash](http://technet.microsoft.com/library/hh847780.aspx), dove la chiave della tabella hash corrisponde al nome del parametro e il valore al valore del parametro. L'esempio seguente illustra come avviare un runbook con due parametri di stringa denominati FirstName e LastName, un parametro di tipo intero denominato RepeatCount e un parametro booleano denominato Show. Per altre informazioni sui parametri, vedere [Parametri di runbook](#Runbook-parameters) più avanti.

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Parametri di runbook

Quando si avvia un runbook mediante il portale di gestione di Azure o Windows PowerShell, l'istruzione viene inviata attraverso il servizio Web Automazione di Azure. Questo servizio non supporta i parametri con tipi di dati complessi. Se è necessario specificare un valore per un parametro complesso, eseguire la chiamata inline da un altro runbook come descritto in [Avvio di un runbook da un altro runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx).

Il servizio Web Automazione di Azure offrirà funzionalità speciali per i parametri che usano determinati tipi di dati, come descritto nelle sezioni seguenti.

### Valori denominati

Se il parametro è un tipo di dati [object], è possibile usare il formato JSON seguente per inviargli un elenco di valori denominati: *{"Name1":Value1, "Name2":Value2, "Name3":Value3}*. Questi valori devono essere tipi semplici. Il runbook riceverà il parametro come [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) con proprietà che corrispondono a ogni valore denominato.

Si consideri il runbook di test seguente che accetta un parametro denominato user.

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

È possibile usare il testo seguente per il parametro user.

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

Si ottiene l'output seguente.

	Joe
	Smith
	Joe
	Smith

### Matrici

Se il parametro è una matrice, ad esempio [array] o [string[]], è possibile usare il formato JSON seguente per inviargli un elenco di valori: *[Value1,Value2,Value3]*. Questi valori devono essere tipi semplici.

Si consideri il runbook di test seguente che accetta un parametro denominato *user*.

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

È possibile usare il testo seguente per il parametro user.

	["Joe","Smith",2,true]

Si ottiene l'output seguente.

	Joe
	Smith
	Joe
	Smith

### Credenziali

Se il parametro è un tipo di dati **PSCredential**, è possibile specificare il nome di un [asset credenziali](http://msdn.microsoft.com/library/azure/dn940015.aspx) di Automazione di Azure. Il runbook recupererà le credenziali con il nome specificato.

Si consideri il runbook di test seguente che accetta un parametro denominato credential.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

È possibile usare il testo seguente per il parametro user presumendo l'esistenza di un asset credenziali denominato *My Credential*.

	My Credential

Presupponendo che il nome utente nelle credenziali sia *jsmith*, si ottiene l'output seguente.

	jsmith

## Articoli correlati

- [Avvio di un runbook da un altro runbook](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=58--> 