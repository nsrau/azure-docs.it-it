---
title: Risoluzione dei problemi relativi ai runbook di Automazione di Azure
description: Informazioni su come risolvere i problemi che potrebbero verificarsi con i runbook di Automazione di Azure.Learn how to troubleshoot and resolve issues you might encounter with Azure Automation runbooks.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 26c5c5b31d5f3f9e1a642c0bafb947190e479055
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632623"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Risoluzione dei problemi relativi ai runbook

Quando si verificano errori durante l'esecuzione di runbook in Automazione di Azure, è possibile usare i passaggi seguenti per diagnosticare i problemi.

1. **Assicurarsi che lo script del runbook venga eseguito correttamente nel computer locale.** 

    Fare riferimento a Documenti di [PowerShell](/powershell/scripting/overview) o [Documenti Python](https://docs.python.org/3/) per i moduli di riferimento e apprendimento del linguaggio. L'esecuzione dello script in locale può individuare e risolvere errori comuni, ad esempio:Executing your script locally can discover and resolve common errors, such as:

      * Moduli mancanti
      * Errori di sintassi
      * Errori logici

2. **Esaminare [i flussi di errore](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)del runbook .**

    Esaminare questi flussi per messaggi specifici e confrontarli con gli errori documentati in questo articolo.

3. **Assicurarsi che i nodi e l'area di lavoro di automazione dispongano dei moduli necessari.** 

    Se il runbook importa dei moduli, verificare che siano disponibili per l'account Automation utilizzando la procedura elencata in [Importa moduli](../shared-resources/modules.md#importing-modules). Aggiornare i moduli alla versione più recente seguendo le istruzioni in Aggiornare i moduli di [Azure in Automazione di Azure.](..//automation-update-azure-modules.md) Per ulteriori informazioni sulla risoluzione dei problemi, consultate [Risoluzione dei problemi relativi ai moduli.](shared-resources.md#modules)

4. **Eseguire questa operazione se il runbook è sospeso o si verifica un errore imprevisto.**

    * [Controlla stati dei processi](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definisce gli stati del runbook e alcune possibili cause.
    * [Aggiungere ulteriore output](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) al runbook per identificare cosa accade prima della sospensione del runbook.
    * [Gestire tutte le eccezioni](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) generate dal processo.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: eseguire Login-AzureRMAccount per accedereScenario: Run Login-AzureRMAccount to log in

### <a name="issue"></a>Problema

Viene visualizzato il seguente errore durante l'esecuzione di un runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi quando non si utilizza un account RunAs o l'account RunAs è scaduto. Vedere [Gestire gli account RunAs di Automazione di Azure.](https://docs.microsoft.com/azure/automation/manage-runas-account)

Questo errore ha due cause principali:

* Esistono versioni diverse del modulo AzureRM o Az.There are different versions of the AzureRM or Az module.
* Si sta tentando di accedere alle risorse in una sottoscrizione separata.

### <a name="resolution"></a>Risoluzione

Se viene visualizzato questo errore dopo l'aggiornamento di un modulo AzureRM o Az, è necessario aggiornare tutti i moduli alla stessa versione.

Se si sta tentando di accedere alle risorse in un'altra sottoscrizione, è possibile seguire la procedura seguente per configurare le autorizzazioni.

1. Passare all'account RunAs di automazione e copiare l'ID applicazione e l'identificazione personale.
  ![Copia ID applicazione e identificazione personale](../media/troubleshoot-runbooks/collect-app-id.png)
1. Passare al controllo di accesso della sottoscrizione in cui l'account di automazione NON è ospitato e aggiungere una nuova assegnazione di ruolo.
  ![Controllo di accesso](../media/troubleshoot-runbooks/access-control.png)
1. Aggiungere l'ID applicazione raccolto in precedenza. Selezionare Autorizzazioni collaboratore.
   ![Aggiungi un'assegnazione di ruolo](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Copiare il nome della sottoscrizione.
1. È ora possibile usare il codice del runbook seguente per testare le autorizzazioni dall'account di automazione all'altra sottoscrizione. Sostituire `"\<CertificateThumbprint\>"` con il valore copiato nel passaggio 1. Sostituire `"\<SubscriptionName\>"` con il valore copiato nel passaggio 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: Non è possibile trovare la sottoscrizione di Azure

### <a name="issue"></a>Problema

Viene visualizzato il seguente errore `Select-AzureSubscription` `Select-AzureRmSubscription` quando si utilizza il cmdlet o :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Errore

Questo errore si verifica nei seguenti casi:

* Il nome della sottoscrizione non è valido.
* L'utente di Azure Active Directory che sta tentando di ottenere i dettagli della sottoscrizione non è configurato come amministratore della sottoscrizione.

### <a name="resolution"></a>Risoluzione

Seguire i passaggi seguenti per determinare se è stata eseguita l'autenticazione in Azure e si ha accesso alla sottoscrizione che si sta tentando di selezionare.

1. Per assicurarsi che lo script funzioni in modo autonomo, testarlo all'esterno di Automazione di Azure.To make sure that your script works standalone, test it outside of Azure Automation.
2. Assicurarsi che lo `Add-AzureAccount` script esegua il cmdlet prima di eseguire il `Select-AzureSubscription` cmdlet.
3. Aggiungere `Disable-AzureRmContextAutosave –Scope Process` all'inizio del runbook. Questa chiamata al cmdlet garantisce che tutte le credenziali si applichino solo all'esecuzione del runbook corrente.
4. Se questo messaggio di errore continua a `AzureRmContext` essere visualizzato, modificare il codice aggiungendo il parametro per il `Add-AzureAccount` cmdlet e quindi eseguire il codice.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: L'autenticazione in Azure non è riuscita perché è abilitata l'autenticazione a più fattori

### <a name="issue"></a>Problema

Durante l'autenticazione in Azure con il nome utente e la password di Azure viene visualizzato l'errore seguente:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se nell'account Azure è abilitata l'autenticazione a più fattori, non è possibile usare un utente di Azure Active Directory per l'autenticazione in Azure. È invece necessario usare un certificato o un'entità servizio per l'autenticazione.

### <a name="resolution"></a>Risoluzione

Per usare un certificato con i cmdlet del modello di distribuzione classica di Azure, vedere Creazione e aggiunta di un certificato per gestire i servizi di Azure.To use a certificate with Azure classic deployment model cmdlets, refer to [Creating and adding a certificate to manage Azure services](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Per usare un'entità servizio con i cmdlet di Azure Resource Manager, vedere Creazione di entità [servizio tramite il portale](../../active-directory/develop/howto-create-service-principal-portal.md) di Azure e [Autenticazione di un'entità servizio con Azure Resource Manager.To use](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)a service principal with Azure Resource Manager cmdlets, see Creating service principal using Azure portal and Authenticating a service principal with Azure Resource Manager .

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: viene visualizzato un errore nei flussi di lavoro relativi al metodo get_SerializationSettings

### <a name="issue"></a>Problema

Viene visualizzato il seguente errore nei flussi di lavoro per un runbook:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>Causa

Questo errore è causato dall'utilizzo di entrambi i cmdlet modulo AzureRM e Az in un runbook. Si verifica quando si importa il modulo Az prima di importare il modulo AzureRM.

### <a name="resolution"></a>Risoluzione

I cmdlet di Az e AzureRM non possono essere importati e usati nello stesso runbook. Per altre informazioni sui cmdlet Az in Automazione di Azure, vedere Supporto dei [moduli di Az in Automazione di Azure.To](../az-modules.md)learn more about Az cmdlets in Azure Automation, see Az module support in Azure Automation .

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scenario: il runbook ha esito negativo con errore "Un'attività è stata annullata"

### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Questo errore può essere causato dall'utilizzo di moduli di Azure non aggiornati.

### <a name="resolution"></a>Risoluzione

È possibile risolvere questo errore aggiornando i moduli di Azure alla versione più recente. 

1. Nell'account di automazione fare clic su **Moduli**, quindi **su Aggiorna moduli di Azure**. 
2. L'aggiornamento dura circa 15 minuti. Al termine, eseguire nuovamente il runbook non riuscito.

Per altre informazioni sull'aggiornamento dei moduli, vedere Aggiornare i moduli di [Azure in Automazione di Azure.To](../automation-update-azure-modules.md)learn more about updating your modules, see Update Azure modules in Azure Automation.

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbook ha esito negativo quando si gestiscono più sottoscrizioniScenario: Runbooks fail when dealing with multiple subscriptions

### <a name="issue"></a>Problema

Durante l'esecuzione dei runbook, il runbook non riesce a gestire le risorse di Azure.When executing runbooks, the runbook fails to manage Azure resources.

### <a name="cause"></a>Causa

Il runbook non usa il contesto corretto durante l'esecuzione.

### <a name="resolution"></a>Risoluzione

Il contesto della sottoscrizione potrebbe andare perso quando un runbook richiama più runbook. Per assicurarsi che il contesto della sottoscrizione venga passato ai `Start-AzureRmAutomationRunbook` runbook, `AzureRmContext` fare in modo che il runbook client passi il contesto al cmdlet nel parametro. Utilizzare `Disable-AzureRmContextAutosave` il cmdlet `Scope` con `Process` il parametro impostato per assicurarsi che le credenziali specificate vengano utilizzate solo per il runbook corrente. Per ulteriori informazioni, vedere [Utilizzo di più sottoscrizioni](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: termine non riconosciuto come nome di un cmdlet, funzione, script

### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per i seguenti motivi:

* Il modulo contenente il cmdlet non viene importato nell'account di automazione.
* Il modulo contenente il cmdlet viene importato ma non è aggiornato.

### <a name="resolution"></a>Risoluzione

Eseguire una delle attività seguenti per risolvere l'errore. 

* Per un modulo di Azure, vedere Come aggiornare i moduli di [Azure PowerShell in Automazione](../automation-update-azure-modules.md) di Azure per informazioni su come aggiornare i moduli nell'account di automazione.

* Per un modulo non Azure, assicurarsi che il modulo in importato nell'account di automazione.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: l'avvio del processo del runbook è stato tentato tre volte, ma non è stato avviato ogni volta

### <a name="issue"></a>Problema

Il runbook ha esito negativo con il seguente errore:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Questo errore si verifica a causa di uno dei seguenti problemi:

* Limite di memoria. Un processo potrebbe non riuscire se utilizza più di 400 MB di memoria. I limiti documentati per la memoria allocata a una sandbox si trovano in Limiti del [servizio di automazione](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Socket di rete. I Sandbox di Azure sono limitati a 1000 socket di rete simultanei. Vedere [Limiti del servizio di automazione](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modulo incompatibile. Le dipendenze dei moduli potrebbero non essere corrette. In questo caso, il runbook restituisce in genere un `Command not found` messaggio o `Cannot bind parameter` .

* Nessuna autenticazione con Active Directory per sandbox. Il runbook ha tentato di chiamare un eseguibile o un sottoprocesso in esecuzione in una sandbox di Azure.Your runbook attempted to call an executable or subprocess that runs in an Azure sandbox. La configurazione dei runbook per l'autenticazione con Azure AD tramite Azure Active Directory Authentication Library (ADAL) non è supportata.

* Troppi dati di eccezione. Il runbook ha tentato di scrivere troppi dati di eccezione nel flusso di output.

### <a name="resolution"></a>Risoluzione

* Limite di memoria, Socket di rete. I modi suggeriti per lavorare entro i limiti di memoria sono suddividere il carico di lavoro tra più runbook, elaborare meno dati in memoria, evitare di scrivere output non necessario dai runbook e considerare quanti checkpoint vengono scritti nei runbook del flusso di lavoro di PowerShell.Suggestd ways to work within the memory limits is to split the workload among multiple runbooks, process less data in memory, avoid writing unnecessary output from your runbooks,, and consider how many checkpoints are written into your PowerShell workflow runbooks. Utilizzare il metodo clear, ad `$myVar.clear`esempio , `[GC]::Collect` per cancellare le variabili e utilizzarle per eseguire immediatamente la procedura di Garbage Collection. in modo da ridurre il footprint della memoria durante l'esecuzione dei runbook.

* Modulo incompatibile. Aggiornare i moduli di Azure seguendo la procedura descritta in Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.Update](../automation-update-azure-modules.md)your Azure modules by following the steps in How to update Azure PowerShell modules in Azure Automation .

* Nessuna autenticazione con Active Directory per Sandbox. Quando si esegue l'autenticazione in Azure AD con un runbook, verificare che il modulo di Azure AD sia disponibile nell'account di automazione. Assicurarsi di concedere all'account RunAs le autorizzazioni necessarie per eseguire le attività automatizzate dal runbook.

  Se il runbook non è in grado di chiamare un eseguibile o un sottoprocesso in esecuzione in una sandbox di Azure, usare il runbook in un ruolo di lavoro ibrido per [runbook.](../automation-hrw-run-runbooks.md) I ruoli di lavoro ibridi non sono limitati dai limiti di memoria e rete dei Sandbox di Azure.Hybrid workers aren't limited by the memory and network limits that Azure sandboxes have.

* Troppi dati di eccezione. Esiste un limite di 1 MB per il flusso di output del processo. Assicurarsi che il runbook comcosti le `try` chiamate `catch` a un eseguibile o a un sottoprocesso usando e blocca. Se le operazioni generano un'eccezione, fare in modo che il codice scriva il messaggio dall'eccezione in una variabile di automazione. Questa tecnica impedisce la scrittura del messaggio nel flusso di output del processo.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: accesso all'account di Azure non riuscitoScenario: Sign-in to Azure account failed

### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti errori `Add-AzureAccount` `Connect-AzureRmAccount` quando si utilizza il cmdlet o :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Questi errori si verificano se il nome dell'asset delle credenziali non è valido. Possono verificarsi anche se il nome utente e la password usati per configurare l'asset delle credenziali di automazione non sono validi.

### <a name="resolution"></a>Risoluzione

Per determinare la causa del problema, seguire questa procedura:

1. Assicurati di non avere caratteri speciali. ad esempio il carattere `\@` nel nome dell'asset delle credenziali di automazione usato per connettersi ad Azure.
2. Verificare se è possibile usare il nome utente e la password archiviati nelle credenziali di Automazione di Azure nell'editor di PowerShell ISE locale. Eseguire i cmdlet seguenti in PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se l'autenticazione non riesce in locale, le credenziali di Azure Active Directory non sono configurate correttamente. Fare riferimento al post di blog [sull'autenticazione in Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) per configurare correttamente l'account di Azure Active Directory.Refer to the Authenticating to Azure using Azure Active Directory blog post to get the Azure Active Directory account set up correctly.

4. Se l'errore sembra essere temporaneo, provare ad aggiungere la logica di ripetizione dei tentativi alla routine di autenticazione per rendere più affidabile l'autenticazione.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: riferimento all'oggetto non impostato su un'istanza di un oggetto

### <a name="issue"></a>Problema

Viene visualizzato il seguente errore quando si `Wait` richiama un runbook figlio con il parametro e il flusso di output contiene un oggetto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

`Start-AzureRmAutomationRunbook`non gestisce correttamente il flusso di output se il flusso contiene oggetti.

### <a name="resolution"></a>Risoluzione

È consigliabile implementare una logica di polling e usare il cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) per recuperare l'output. Di seguito è riportato un esempio di questa logica.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook con esito negativo a causa di un oggetto deserializzato

### <a name="issue"></a>Problema

Il runbook ha esito negativo con errore:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se il runbook è un flusso di lavoro di PowerShell, archivia gli oggetti complessi in un formato deserializzato per rendere persistente lo stato del runbook quando il flusso di lavoro viene sospeso.

### <a name="resolution"></a>Risoluzione

Utilizzare una delle seguenti soluzioni per risolvere il problema.

* Se si esegue il piping di oggetti complessi da un `InlineScript` cmdlet a un altro, eseguire il wrapping di questi cmdlet in un'attività.
* Passare il nome o il valore necessario dall'oggetto complesso invece di passare l'intero oggetto.
* Usare un runbook di PowerShell invece di un runbook del flusso di lavoro PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenario: il processo runbook non riesce perché la quota allocata è stata superata

### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Questo errore si verifica quando l'esecuzione del processo supera la quota disponibile di 500 minuti per l'account. Questa quota si applica a tutti i tipi di attività di esecuzione di processi, Alcune di queste attività sono il test di un processo, l'avvio di un processo dal portale, l'esecuzione di un processo tramite webhook o la pianificazione di un processo da eseguire tramite il portale di Azure o il data center. Per altre informazioni sui prezzi relativi all'automazione, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Risoluzione

Se si desidera usare più di 500 minuti di elaborazione al mese, modificare la sottoscrizione dal livello Gratuito al livello Basic.If you want to use more than 500 minutes of processing per month, change your subscription from the Free tier to the Basic tier.

1. Accedere alla sottoscrizione di Azure.
2. Selezionare l'account di automazione da aggiornare.
3. Fare clic su **Impostazioni**, quindi **su Prezzi**.
4. Fare clic su **Abilita** nella pagina inferiore per aggiornare l'account al livello Basic.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: Cmdlet non riconosciuto durante l'esecuzione di un runbook

### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando il motore di PowerShell non trova il cmdlet utilizzato nel runbook. È possibile che il modulo contenente il cmdlet non sia presente nell'account, che si sia verificato un conflitto di nomi con un nome di runbook oppure che il cmdlet esista anche in un altro modulo e che l'automazione non sia in grado di risolvere il nome.

### <a name="resolution"></a>Risoluzione

Utilizzare una delle seguenti soluzioni per risolvere il problema.

* Assicurarsi di aver immesso correttamente il nome del cmdlet.
* Verificare che il cmdlet esista nell'account di automazione e che non siano presenti conflitti. Per verificare se il cmdlet è presente, aprire un runbook in modalità di `Get-Command <CommandName>`modifica e cercare il cmdlet che si desidera trovare nella libreria oppure eseguire . Dopo aver convalidato che il cmdlet è disponibile per l'account e che non sono presenti conflitti di nomi con altri cmdlet o runbook, aggiungere il cmdlet all'area di disegno e assicurarsi di utilizzare un set di parametri valido nel runbook.
* Se si verifica un conflitto di nomi e il cmdlet è disponibile in due moduli diversi, risolvere il problema utilizzando il nome completo per il cmdlet. È ad esempio possibile usare `ModuleName\CmdletName`.
* Se si esegue il runbook locale in un gruppo di lavoro ibrido, assicurarsi che il modulo e il cmdlet siano installati nel computer che ospita il worker ibrido.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: un runbook a esecuzione prolungata non viene completato

### <a name="issue"></a>Problema

Il runbook mostra lo stato Stopped (Arrestato) dopo 3 ore di esecuzione. È inoltre possibile che venga visualizzato questo errore:You might also come this error:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Questo comportamento è legato nelle sandbox di Azure a causa del monitoraggio della quota equa dei processi all'interno di Automazione di Azure.This behavior is by design in Azure sandboxes because of the [fair share](../automation-runbook-execution.md#fair-share) monitoring of processes within Azure Automation. Se un processo viene eseguito più di tre ore, la condivisione equa arresta automaticamente un runbook. Lo stato di un runbook che supera il limite di tempo della condivisione equa varia in base al tipo di libro di esecuzione. I runbook PowerShell e Python sono impostati sullo stato Stopped (Arrestato). I runbook PowerShell del flusso di lavoro sono impostati su Failed (Non riuscito).

### <a name="cause"></a>Causa

Il runbook ha superato il limite di 3 ore consentito dalla condivisione equa in una sandbox di Azure.The runbook ran over the 3-hour limit allowed by fair share in an Azure sandbox.

### <a name="resolution"></a>Risoluzione

Una soluzione consigliata consiste nell'eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non sono limitati dal limite di runbook di condivisione equa di 3 ore di 3 ore di cui dispongono i Sandbox di Azure.Hybrid Workers aren't limited by the 3-hour fair share runbook limit that Azure sandboxes have. Runbooks eseguiti su Hybrid Runbook I ruoli di lavoro devono essere sviluppati per supportare i comportamenti di riavvio in caso di problemi imprevisti dell'infrastruttura locale.

Un'altra soluzione consiste nell'ottimizzare il runbook creando [runbook figlio.](../automation-child-runbooks.md) Se il runbook scorre in ciclo la stessa funzione su più risorse, ad esempio in un'operazione di database su più database, è possibile spostare la funzione in un runbook figlio. Ogni runbook figlio viene eseguito in parallelo in un processo separato. Questo comportamento riduce la quantità totale di tempo per il completamento del runbook padre.

I cmdlet di PowerShell che consentono lo scenario del runbook figlio sono:

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook). questo cmdlet consente di avviare un runbook e trasmettergli i parametri.

* [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Se sono presenti operazioni che devono essere eseguite dopo il completamento del runbook figlio, questo cmdlet consente di controllare lo stato del processo per ogni figlio.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: stato: 400 richiesta non valida quando si chiama un webhook

### <a name="issue"></a>Problema

Quando si tenta di richiamare un webhook per un runbook di Automazione di Azure, viene visualizzato il seguente errore:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

Il webhook che si sta tentando di chiamare è disabilitato o scaduto.

### <a name="resolution"></a>Risoluzione

Se il webhook è disattivato, è possibile riabilitarlo tramite il portale di Azure. Se il webhook è scaduto, è necessario eliminarlo e ricrearlo. Il [rinnovo di un webhook](../automation-webhooks.md#renew-webhook) è possibile solo se il webhook non è scaduto.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: la frequenza delle richieste è attualmente troppo grande...

### <a name="issue"></a>Problema

Quando si esegue il cmdlet `Get-AzureRmAutomationJobOutput` viene visualizzato il messaggio di errore seguente:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Questo errore può verificarsi quando si recupera l'output del processo da un runbook con molti [flussi verbose](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Risoluzione

Eseguire una delle operazioni seguenti per risolvere l'errore.

* Modificare il runbook riducendo il numero di flussi del processo generati dal runbook stesso.

* Ridurre il numero di flussi da recuperare all'esecuzione del cmdlet. A tale scopo, è possibile `Stream` impostare `Get-AzureRmAutomationJobOutput` il valore del parametro per il cmdlet per recuperare solo i flussi di output. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenario: processo di PowerShell ha esito negativo con errore: Impossibile richiamare il metodoScenario: PowerShell job fails with error: Cannot invoke method

### <a name="issue"></a>Problema

Viene visualizzato il seguente messaggio di errore quando si avvia un processo di PowerShell in un runbook in esecuzione in Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Questo errore potrebbe indicare che non è possibile eseguire runbook in esecuzione in una sandbox di Azure in [modalità in linguaggio completo.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Risoluzione

Esistono due modi per risolvere questo errore.

* Anziché `Start-Job`utilizzare `Start-AzureRmAutomationRunbook` , utilizzare per avviare il runbook.
* Provare a eseguire il runbook in un ruolo di lavoro ibrido per runbook.

Per altre informazioni su questo comportamento e altri comportamenti dei runbook di Automazione di Azure, vedere Comportamento di [Runbook.](../automation-runbook-execution.md#runbook-behavior)

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenario: Linux Hybrid Runbook Worker riceve una richiesta di password per la firma di un runbook

### <a name="issue"></a>Problema

L'esecuzione del `sudo` comando per un ruolo di lavoro ibrido ibrido Linux recupera una richiesta imprevista di una password.

### <a name="cause"></a>Causa

L'account **nxautomationuser** per l'agente log Analytics per Linux non è configurato correttamente nel file **sudoers.** The Hybrid Runbook Worker needs the appropriate configuration of account permissions and other data so that it can sign runbooks on the Linux Runbook Worker.

### <a name="resolution"></a>Risoluzione

* Assicurarsi che il lavoratore runbook ibrido disponga dell'eseguibile GnuPG (GPG) nel computer.

* Verificare la configurazione dell'account **nxautomationuser** nel file **sudoers.** Vedere [Esecuzione di runbook in un](../automation-hrw-run-runbooks.md) ruolo di lavoro ibrido per runbook

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: Cmdlet failing in PnP PowerShell runbook on Azure Automation

### <a name="issue"></a>Problema

Quando un runbook scrive direttamente un oggetto generato da PnP PowerShell nell'output di Automazione di Azure, l'output del cmdlet non può essere eseguito nuovamente all'automazione.

### <a name="cause"></a>Causa

Questo problema si verifica in genere quando l'automazione di Azure elabora i `add-pnplistitem`runbook che richiamano i cmdlet Di PowerShell PnP, ad esempio , senza intercettare gli oggetti restituiti.

### <a name="resolution"></a>Risoluzione

Modificare gli script per assegnare valori restituiti alle variabili in modo che i cmdlet non tentino di scrivere interi oggetti nell'output standard. Uno script può reindirizzare il flusso di output a un cmdlet, come illustrato di seguito.

```azurecli
  $null = add-pnplistitem
```

Se lo script analizza l'output del cmdlet, lo script deve archiviare l'output in una variabile e modificare la variabile anziché semplicemente trasmettere l'output.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>il problema non è incluso in questo elenco

Le sezioni seguenti elencano altri errori comuni e forniscono la documentazione di supporto per risolvere il problema.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Il ruolo di lavoro per runbook ibrido non esegue i processi o non risponde

Se si eseguono processi in un ruolo di lavoro ibrido per runbook anziché in Automazione di Azure, potrebbe essere necessario risolvere i problemi relativi al [ruolo di lavoro ibrido stesso.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook ha esito negativo con nessuna autorizzazione o qualche varianteRunbook fails with No permission or some variation

Gli account RunAs potrebbero non disporre delle stesse autorizzazioni per le risorse di Azure dell'account corrente. Assicurarsi che l'account RunAs disponga [delle autorizzazioni per accedere alle risorse](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilizzate nello script.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemi relativi al passaggio di parametri nei webhook

Per informazioni sul passaggio di parametri in webhook, vedere [Avviare un runbook da un webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemi relativi all'utilizzo dei moduli Az

L'uso di moduli Az e AzureRM nello stesso account di automazione non è supportato. Per ulteriori dettagli, vedere [Moduli di Ache nei runbook.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="inconsistent-behavior-in-runbooks"></a>Comportamento incoerente nei runbook

Seguire le indicazioni in [Esecuzione di Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) per evitare problemi con processi simultanei, risorse create più volte o altra logica sensibile alla temporizzazione nei runbook.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook ha esito negativo con l'errore Nessuna autorizzazione, Accesso negato (403) o alcune variantiRunbook fails with the error No permission, Forbidden (403) or some variation

Gli account RunAs potrebbero non disporre delle stesse autorizzazioni per le risorse di Azure dell'account corrente. Assicurarsi che l'account RunAs disponga [delle autorizzazioni per accedere alle risorse](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilizzate nello script.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>I runbook funzionavano ma sono stati arrestati improvvisamente

* Assicurarsi che l'account RunAs non sia scaduto. Vedere [rinnovo della certificazione](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se si usa un [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) per avviare un runbook, verificare che il webhook non sia scaduto.

### <a name="passing-parameters-into-webhooks"></a>Trasmissione dei parametri nei webhook

Per informazioni sul passaggio di parametri in webhook, vedere [Avviare un runbook da un webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Uso dei moduli Az

L'uso di moduli Az e AzureRM nello stesso account di automazione non è supportato. Consultate [Moduli Az nei runbook.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="using-self-signed-certificates"></a>Utilizzo di certificati autofirmati

Per utilizzare certificati autofirmati, vedere [Creazione di un nuovo certificato](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Accesso negato quando si usa la sandbox di Azure per un runbookAccess denied when using Azure sandbox for a runbook

La sandbox di Azure impedisce l'accesso a tutti i server COM out-of-process. Ad esempio, un'applicazione in modalità sandbox o un runbook non può chiamare Strumentazione gestione Windows (WMI) o nel servizio Windows Installer (msiserver.exe). Per informazioni dettagliate sull'uso della sandbox, vedere Esecuzione di [Runbook in Automazione](https://docs.microsoft.com/azure/automation/automation-runbook-execution)di Azure.

## <a name="recommended-documents"></a>Documenti consigliati

* [Avvio di un runbook in Automazione di AzureStarting a runbook in Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook execution in Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
