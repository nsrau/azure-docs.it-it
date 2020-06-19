---
title: Risolvere i problemi relativi ai runbook di Automazione di Azure
description: Questo articolo descrive come risolvere i problemi relativi ai runbook di Automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.custom: has-adal-ref
ms.openlocfilehash: 1ee6920d1870b7449f4b77394aaf918947f57ea5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744320"
---
# <a name="troubleshoot-runbook-issues"></a>Risolvere i problemi relativi ai runbook

 Questo articolo descrive i problemi che possono verificarsi quando si eseguono i runbook e offre indicazioni su come risolverli. Per informazioni generali, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnosticare i problemi dei runbook

Quando vengono visualizzati errori durante l'esecuzione dei runbook in Automazione di Azure, è possibile seguire questa procedura per diagnosticare i problemi:

1. Verificare che lo script dei runbook venga eseguito correttamente nel computer locale.

    Per le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](/powershell/scripting/overview) o la [documentazione di Python](https://docs.python.org/3/). Quando si esegue lo script localmente, è possibile individuare e risolvere errori comuni, ad esempio quelli relativi a:

      * Moduli mancanti
      * Errori di sintassi
      * Errori di logica

1. Esaminare i [flussi di errore](../automation-runbook-output-and-messages.md#runbook-output) dei runbook.

    Esaminare i flussi di errore per individuare specifici messaggi e confrontarli con gli errori descritti nell'articolo.

1. Verificare che per i nodi e per l'area di lavoro di Automazione siano disponibili i moduli necessari.

    Se il runbook importa tutti i moduli, verificare che siano disponibili per l'account di Automazione seguendo la procedura descritta in [Importare i moduli](../shared-resources/modules.md#import-modules). Aggiornare i moduli PowerShell alla versione più recente seguendo le istruzioni riportate in [Aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md). Per altre informazioni sulla risoluzione di problemi, vedere [Risolvere i problemi relativi ai moduli](shared-resources.md#modules).

1. Se il runbook viene sospeso o termina in modo imprevisto:

    * [Rinnovare il certificato](../manage-runas-account.md#cert-renewal) se l'account RunAs è scaduto.
    * [Rinnovare il webhook](../automation-webhooks.md#renew-a-webhook) se si sta provando a usare un webhook scaduto per avviare il runbook.
    * [Controllare gli stati dei processi](../automation-runbook-execution.md#job-statuses) per determinare gli stati attuali del runbook e individuare alcune possibili cause del problema.
    * [Aggiungere altro output](../automation-runbook-output-and-messages.md#monitor-message-streams) al runbook per stabilire cosa succede prima che il runbook venga sospeso.
    * [Gestire tutte le eccezioni](../automation-runbook-execution.md#exceptions) generate dal processo.

1. Eseguire questo passaggio se il processo del runbook o l'ambiente nel ruolo di lavoro ibrido per runbook non risponde.

    Se i runbook vengono eseguiti in un ruolo di lavoro ibrido per runbook invece che in Automazione di Azure, potrebbe essere necessario [risolvere i problemi relativi al ruolo di lavoro ibrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenario: il runbook ha esito negativo e viene visualizzato l'errore 403 indicante operazione non consentita o autorizzazione mancante

### <a name="issue"></a>Problema

Il runbook ha esito negativo e viene visualizzato l'errore 403 per indicare che manca l'autorizzazione necessaria, l'operazione non è consentita o un errore equivalente.

### <a name="cause"></a>Causa

Le autorizzazioni degli account RunAs per le risorse di Azure potrebbero non essere uguali a quelle dell'account di Automazione corrente. 

### <a name="resolution"></a>Risoluzione

Verificare che l'account RunAs abbia le [autorizzazioni per accedere a tutte le risorse](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usate nello script.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: accesso all'account di Azure non riuscito

### <a name="issue"></a>Problema

Quando si usa il cmdlet `Connect-AzAccount`, viene visualizzato uno degli errori seguenti:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Questi errori si verificano se il nome dell'asset delle credenziali non è valido o se non sono validi il nome utente e la password usati per impostare l'asset delle credenziali di Automazione.

### <a name="resolution"></a>Risoluzione

Per individuare la causa del problema, seguire questa procedura:

1. Verificare che non siano stati inclusi caratteri speciali, ad esempio `\@`, nel nome dell'asset delle credenziali di Automazione usato per connettersi ad Azure.
1. Verificare di poter usare il nome utente e la password archiviati nelle credenziali di Automazione di Azure nell'editor locale di PowerShell ISE. In PowerShell ISE digitare i cmdlet seguenti.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Se l'autenticazione ha esito negativo localmente, le credenziali di Azure Active Directory (Azure AD) non sono state configurate correttamente. Per ottenere la configurazione dell'account Azure AD corretta, vedere il post di blog [Autenticazione in Azure tramite Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).

1. L'errore potrebbe essere temporaneo. In caso, provare ad aggiungere la logica di ripetizione alla routine di autenticazione per rendere più affidabile l'autenticazione.

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: eseguire Login-AzureRmAccount per accedere

### <a name="issue"></a>Problema

Quando si esegue un runbook, viene visualizzato l'errore seguente:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi quando non si usa un account RunAs o quando l'account RunAs è scaduto. Per altre informazioni, vedere [Gestire account RunAs di Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Le principali cause di questo errore sono due:

* Sono presenti versioni diverse del modulo AzureRM o Az.
* Si sta provando ad accedere alle risorse in una sottoscrizione separata.

### <a name="resolution"></a>Risoluzione

Se questo errore viene visualizzato dopo l'aggiornamento di un modulo AzureRM o Az, aggiornare tutti i moduli alla stessa versione.

Se si sta provando ad accedere alle risorse in un'altra sottoscrizione, configurare le autorizzazioni seguendo questa procedura:

1. Accedere all'account RunAs di Automazione e copiare i valori di **ID applicazione** e **Identificazione personale**.

    ![Copiare ID applicazione e Identificazione personale](../media/troubleshoot-runbooks/collect-app-id.png)

1. Accedere alla pagina **Controllo di accesso** della sottoscrizione che *non* ospita l'account di Automazione e aggiungere una nuova assegnazione di ruolo.

    ![Controllo di accesso](../media/troubleshoot-runbooks/access-control.png)

1. Aggiungere il valore di **ID applicazione** copiato in precedenza. Selezionare le autorizzazioni di **Collaboratore**.

    ![Aggiungi un'assegnazione di ruolo](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Copiare il nome della sottoscrizione.

1. È ora possibile usare il codice del runbook seguente per testare le autorizzazioni dell'account di Automazione nell'altra sottoscrizione. Sostituire `"\<CertificateThumbprint\>"` con il valore copiato nel passaggio 1. Sostituire `"\<SubscriptionName\>"` con il valore copiato nel passaggio 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: non è possibile trovare la sottoscrizione di Azure

### <a name="issue"></a>Problema

Quando si usa il cmdlet `Select-AzureSubscription`, `Select-AzureRMSubscription` o `Select-AzSubscription`, viene visualizzato l'errore seguente:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Errore

Questo errore si verifica nei casi seguenti:

* Il nome della sottoscrizione non è valido.
* L'utente di Azure AD che sta provando a ottenere i dettagli della sottoscrizione non è configurato come amministratore della sottoscrizione.
* Il cmdlet non è disponibile.

### <a name="resolution"></a>Risoluzione

Per accertarsi di essere autenticati ad Azure e poter accedere alla sottoscrizione che si sta provando a selezionare, seguire questa procedura:

1. Per verificare che lo script funzioni autonomamente, testare lo script all'esterno di Automazione di Azure.
1. Verificare che lo script esegua il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) prima di eseguire il cmdlet `Select-*`.
1. Aggiungere `Disable-AzContextAutosave –Scope Process` all'inizio del runbook. Questo cmdlet garantisce che eventuali credenziali vengano applicate solo all'esecuzione del runbook corrente.
1. Se il messaggio di errore è ancora visibile, modificare il codice aggiungendo il parametro `AzContext` per `Connect-AzAccount` e quindi eseguire il codice.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: i runbook hanno esito negativo quando si gestiscono più sottoscrizioni

### <a name="issue"></a>Problema

Durante l'esecuzione dei runbook, il runbook non riesce a gestire le risorse di Azure.

### <a name="cause"></a>Causa

Il runbook non usa il contesto corretto durante l'esecuzione.

### <a name="resolution"></a>Risoluzione

Quando un runbook richiama più runbook, è possibile che il contesto della sottoscrizione venga perso. Affinché il contesto della sottoscrizione venga passato ai runbook, è necessario che il runbook client passi il contesto al cmdlet `Start-AzureRmAutomationRunbook` nel parametro `AzureRmContext`. Usare il cmdlet `Disable-AzureRmContextAutosave` con il parametro `Scope` impostato su `Process` per assicurarsi che le credenziali specificate vengano usate solo per il runbook corrente. Per altre informazioni, vedere [Sottoscrizioni](../automation-runbook-execution.md#subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: l'autenticazione in Azure ha esito negativo perché è abilitata l'autenticazione a più fattori

### <a name="issue"></a>Problema

Quando si esegue l'autenticazione ad Azure usando il nome utente e la password di Azure, viene visualizzato l'errore seguente:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se per l'account di Azure è abilitata l'autenticazione a più fattori, non è possibile usare un utente di Azure Active Directory per eseguire l'autenticazione ad Azure. È invece necessario usare un certificato o un'entità servizio.

### <a name="resolution"></a>Risoluzione

Per usare un certificato con i cmdlet del modello di distribuzione classica di Azure, vedere [Creazione e aggiunta di un certificato per la gestione dei servizi di Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Per usare un'entità servizio con i cmdlet di Azure Resource Manager, vedere [Creazione di un'entità servizio tramite il portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [Autenticazione di un'entità servizio con Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenario: esito negativo del runbook con messaggio di errore "Un'attività è stata annullata"

### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Questo errore può essere causato dall'utilizzo di moduli di Azure non aggiornati.

### <a name="resolution"></a>Risoluzione

È possibile risolvere questo errore aggiornando i moduli di Azure alla versione più recente:

1. Nell'account di Automazione selezionare **Moduli** e quindi **Aggiorna moduli di Azure**.
1. Per l'aggiornamento sono necessari circa 15 minuti. Al completamento dell'aggiornamento, eseguire di nuovo il runbook di cui era stato segnalato l'esito negativo.

Per ulteriori informazioni sull'aggiornamento dei moduli, vedere [Aggiornare i moduli di Azure in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: il termine non è riconosciuto come nome di cmdlet, funzione o script

### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per i motivi seguenti:

* Il modulo contenente il cmdlet non viene importato nell'account di Automazione.
* Il modulo contenente il cmdlet viene importato ma non è aggiornato.

### <a name="resolution"></a>Risoluzione

Per correggere l'errore, eseguire una di queste operazioni:

* Se il modulo è un modulo di Azure, vedere [Aggiornare i moduli di Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md) per informazioni su come aggiornare i moduli nell'account di Automazione.
* Se il modulo non è un modulo di Azure, verificare che venga importato nel proprio account di Automazione.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: errore del cmdlet nel runbook PowerShell PnP in Automazione di Azure

### <a name="issue"></a>Problema

Quando un runbook scrive un oggetto generato da PowerShell PnP direttamente nell'output di Automazione di Azure, l'output del cmdlet non può essere ritrasmesso in Automazione.

### <a name="cause"></a>Causa

Questo problema si verifica in genere quando Automazione di Azure elabora runbook che richiamano cmdlet di PowerShell PnP, ad esempio `add-pnplistitem`, senza intercettare gli oggetti restituiti.

### <a name="resolution"></a>Risoluzione

Modificare gli script in modo da assegnare tutti i valori restituiti a variabili e impedire quindi che i cmdlet tentino di scrivere oggetti interi nell'output standard. Uno script può reindirizzare la trasmissione dell'output a un cmdlet, come illustrato di seguito.

```azurecli
  $null = add-pnplistitem
```

Se lo script analizza l'output del cmdlet, deve archiviare l'output in una variabile e modificare la variabile anziché limitarsi a trasmettere l'output.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: cmdlet non riconosciuto durante l'esecuzione di un runbook

### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando il motore di PowerShell non trova il cmdlet utilizzato nel runbook. È possibile che il modulo che contiene il cmdlet non sia presente nell'account, che si sia generato un conflitto con il nome di un runbook o che il cmdlet sia incluso anche in un altro modulo e Automazione non possa risolvere il nome.

### <a name="resolution"></a>Risoluzione

Per correggere il problema, scegliere una di queste soluzioni:

* Verificare di aver immesso correttamente il nome del cmdlet.
* Verificare che il cmdlet sia incluso nell'account di Automazione e che non siano presenti conflitti. Per verificare la presenza del cmdlet, aprire un runbook in modalità di modifica e cercare il cmdlet nella libreria o eseguire `Get-Command <CommandName>`. Dopo aver verificato la disponibilità del cmdlet nell'account ed essersi assicurati che non sono presenti conflitti di nome con altri cmdlet o runbook, aggiungere il cmdlet al canvas. Verificare di usare un set di parametri valido nel runbook.
* Se è presente un conflitto di nome e il cmdlet è disponibile in due moduli diversi, risolvere il problema usando il nome completo del cmdlet. È ad esempio possibile usare `ModuleName\CmdletName`.
* Se i runbook vengono eseguiti localmente in un gruppo di ruoli di lavoro ibridi, verificare che il modulo e il cmdlet siano installati nel computer che ospita il ruolo di lavoro ibrido.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: riferimento a un oggetto non corretto nella chiamata ad Add-AzAccount

### <a name="issue"></a>Problema

Questo errore viene visualizzato quando si lavora con `Add-AzAccount`, che è un alias per il cmdlet `Connect-AzAccount`:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Questo errore può verificarsi se il runbook non esegue i passaggi appropriati prima di chiamare `Add-AzAccount` per l'aggiunta dell'account di Automazione. Tra i passaggi necessari è ad esempio incluso l'accesso con un account RunAs. Per informazioni sulle operazioni corrette da eseguire nel runbook, vedere [Esecuzione di runbook in Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: riferimento a un oggetto non impostato su un'istanza di un oggetto

### <a name="issue"></a>Problema

L'errore seguente viene visualizzato quando si chiama un runbook figlio tramite il parametro `Wait` e il flusso di output contiene un oggetto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Se il flusso contiene oggetti, `Start-AzAutomationRunbook` non gestisce correttamente il flusso di output.

### <a name="resolution"></a>Risoluzione

Implementare una logica di polling e usare il cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) per recuperare l'output. Di seguito è riportato un esempio di questa logica:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: il runbook ha esito negativo a causa di un oggetto deserializzato

### <a name="issue"></a>Problema

Il runbook ha esito negativo con errore:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se il runbook è un flusso di lavoro di PowerShell, archivia gli oggetti complessi in un formato deserializzato per rendere persistente lo stato del runbook quando il flusso di lavoro viene sospeso.

### <a name="resolution"></a>Risoluzione

Per correggere il problema, scegliere una di queste soluzioni:

* Se si stanno inviando oggetti complessi da un cmdlet a un altro cmdlet tramite pipe, eseguire il wrapping di questi cmdlet in un'attività `InlineScript`.
* Passare il nome o il valore necessario dall'oggetto complesso invece di passare l'intero oggetto.
* Usare un runbook di PowerShell invece di un runbook del flusso di lavoro PowerShell.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: errore 400 di stato richiesta non valido durante la chiamata di un webhook

### <a name="issue"></a>Problema

Quando si prova a richiamare un webhook per un runbook di Automazione di Azure, viene visualizzato l'errore seguente:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

Il webhook che si sta tentando di chiamare è disabilitato o scaduto. 

### <a name="resolution"></a>Risoluzione

Se il webhook è disattivato, è possibile riabilitarlo tramite il portale di Azure. Se il webhook è scaduto, è necessario eliminarlo e ricrearlo. Il [rinnovo di un webhook](../automation-webhooks.md#renew-a-webhook) è possibile solo se il webhook non è scaduto. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: frequenza richieste troppo elevata

### <a name="issue"></a>Problema

Quando si esegue il cmdlet `Get-AzAutomationJobOutput` viene visualizzato il messaggio di errore seguente:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Questo errore può verificarsi durante il recupero dell'output del processo da un runbook con numerosi [flussi dettagliati](../automation-runbook-output-and-messages.md#monitor-verbose-stream).

### <a name="resolution"></a>Risoluzione

Per correggere l'errore, eseguire una di queste operazioni:

* Modificare il runbook riducendo il numero di flussi del processo generati dal runbook stesso.
* Ridurre il numero di flussi da recuperare all'esecuzione del cmdlet. A tale scopo, è possibile impostare il valore del parametro `Stream` per il cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) in modo che vengano recuperati solo flussi di output. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenario: processo runbook non riuscito per superamento della quota allocata

### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Questo errore si verifica quando l'esecuzione del processo supera la quota disponibile di 500 minuti per l'account. Questa quota si applica a tutti i tipi di attività di esecuzione di processi, ad esempio il test di un processo, l'avvio di un processo dal portale, l'esecuzione di un processo tramite webhook o la pianificazione di un processo da eseguire tramite il portale di Azure o nel proprio data center. Per altre informazioni sui prezzi relativi all'automazione, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Risoluzione

Se si desidera poter usare più di 500 minuti di elaborazione al mese, è necessario modificare la sottoscrizione dal livello gratuito al livello Basic:

1. Accedere alla sottoscrizione di Azure.
1. Selezionare l'account di Automazione da aggiornare.
1. Selezionare **Impostazioni** e quindi **Prezzi**.
1. Selezionare **Abilita** nella parte inferiore della pagina per aggiornare l'account al livello Basic.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: avvio del processo del runbook tentato tre volte con esito negativo ogni volta

### <a name="issue"></a>Problema

Il runbook ha esito negativo e viene visualizzato l'errore seguente:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Questo errore si verifica per uno dei problemi seguenti:

* **Limite di memoria**. Un processo può aver esito negativo se usa più di 400 MB di memoria. I limiti documentati per la quantità di memoria allocata a una sandbox sono riportati in [Limiti del servizio Automazione](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 
* **Socket di rete**. Il limite per le sandbox di Azure è di 1.000 socket di rete simultanei. Per altre informazioni, vedere [Limiti del servizio Automazione](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).
* **Modulo incompatibile**. Le dipendenze del modulo potrebbero non essere corrette. In questo caso, il runbook restituisce in genere un messaggio `Command not found` o `Cannot bind parameter`.
* **Nessuna autenticazione con Active Directory per sandbox**. Il runbook ha tentato di chiamare un eseguibile o un sottoprocesso in esecuzione in una sandbox di Azure. La configurazione dei runbook per l'autenticazione con Azure AD tramite la libreria ADAL (Active Directory Authentication Library) di Azure non è supportata.
* **Troppi dati di eccezione**. Il runbook ha tentato di scrivere una quantità eccessiva di dati di eccezione nel flusso di output.

### <a name="resolution"></a>Risoluzione

* **Limite di memoria, socket di rete**. Per assicurarsi di rispettare i limiti di memoria, è ad esempio consigliabile dividere il carico di lavoro tra diversi runbook, elaborare una quantità minore di dati in memoria, evitare di scrivere di output non necessari dai runbook, tenere in considerazione il numero di checkpoint scritti nei runbook del flusso di lavoro PowerShell. Usare il metodo di cancellazione, ad esempio `$myVar.clear`, per cancellare la variabile e usare `[GC]::Collect` per eseguire immediatamente Garbage Collection, in modo da ridurre il footprint della memoria durante l'esecuzione dei runbook.
* **Modulo incompatibile**. Aggiornare i moduli di Azure eseguendo i passaggi descritti in [Aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).
* **Nessuna autenticazione con Active Directory per sandbox**. Quando si esegue l'autenticazione per Azure AD con un runbook, verificare che il modulo di Azure AD sia disponibile nell'account di Automazione. Assicurarsi di concedere all'account RunAs le autorizzazioni necessarie per eseguire le attività automatizzate dal runbook.

  Se il runbook non è in grado di chiamare un eseguibile o un sottoprocesso in esecuzione in una sandbox di Azure, usare il runbook in un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non sono soggetti ai limiti di memoria e di rete applicati alle sandbox di Azure.

* **Troppi dati di eccezione**. È previsto un limite di 1 MB per il flusso di output di un processo. Verificare che il runbook includa le chiamate a un eseguibile o a un sottoprocesso usando i blocchi `try` e `catch`. Se le operazioni generano un'eccezione, fare in modo che il codice scriva il messaggio generato dall'eccezione in una variabile di Automazione. Grazie a questo accorgimento, il messaggio non verrà scritto nel flusso di output del processo.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenario: il processo di PowerShell ha esito negativo e viene visualizzato il messaggio di errore "Impossibile richiamare il metodo"

### <a name="issue"></a>Problema

Quando si avvia un processo di PowerShell in un runbook in esecuzione in Azure, viene visualizzato il messaggio di errore seguente:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Questo errore può indicare che i runbook in esecuzione in una sandbox di Azure non possono essere eseguiti in [modalità FullLanguage](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Risoluzione

È possibile risolvere questo problema in due modi:

* Anziché [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7), usare [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) per avviare il runbook.
* Provare a eseguire il runbook in un ruolo di lavoro ibrido per runbook.

Per altre informazioni su questo e altri comportamenti dei runbook di Automazione di Azure, vedere [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: mancato completamento di un runbook in esecuzione da molto tempo

### <a name="issue"></a>Problema

Dopo tre ore di esecuzione, il runbook passa nello stato Arrestato. È anche possibile che venga visualizzato questo errore:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Si tratta di un comportamento predefinito nelle sandbox di Azure, determinato dal monitoraggio della [condivisione equa](../automation-runbook-execution.md#fair-share) dei processi in Automazione di Azure. Se un processo viene eseguito per oltre tre ore, la condivisione equa arresta automaticamente il runbook. Lo stato su cui viene impostato un runbook che supera il limite di tempo previsto dalla condivisione equa varia a seconda del tipo di runbook. I runbook PowerShell e Python vengono impostati sullo stato Arrestato. I runbook PowerShell del flusso di lavoro vengono impostati su Non riuscito.

### <a name="cause"></a>Causa

Il runbook ha superato il limite di esecuzione di tre ore consentito dalla condivisione equa in una sandbox di Azure.

### <a name="resolution"></a>Risoluzione

Una soluzione consigliata consiste nell'eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non sono vincolati al limite di tre ore che la condivisione equa impone sui runbook nelle sandbox di Azure. I runbook che vengono eseguiti in ruoli di lavoro ibridi dovrebbero essere sviluppati per supportare comportamenti di riavvio nel caso in cui si verifichino problemi imprevisti nell'infrastruttura locale.

Un'altra soluzione consiste nell'ottimizzare il runbook creando [runbook figlio](../automation-child-runbooks.md). Se il runbook esegue la stessa funzione in ciclo in più risorse, ad esempio un'operazione di database in più database, è possibile spostare tale funzione in un runbook figlio. Ciascun runbook figlio verrà eseguito in parallelo in un processo separato. Questo comportamento riduce la quantità totale di tempo per il completamento del runbook padre.

I cmdlet di PowerShell che consentono lo scenario del runbook figlio sono:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0), questo cmdlet consente di avviare un runbook e trasmettergli i parametri.
* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0), questo cmdlet consente di controllare lo stato del processo per ogni runbook figlio e si rivela utile qualora siano presenti operazioni che devono essere eseguite dopo il completamento di un runbook figlio.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: errore nei flussi del processo per il metodo get_SerializationSettings

### <a name="issue"></a>Problema

Nei flussi di processo relativi a un runbook viene visualizzato l'errore seguente:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Causa

Questo errore si è probabilmente verificato perché è stata usata una migrazione incompleta da AzureRM ai moduli Az nel runbook. A causa di questa situazione, è possibile che Automazione di Azure avvii un processo del runbook usando solo moduli AzureRM e quindi un altro processo usando solo moduli Az, con un conseguente arresto anomalo della sandbox.

### <a name="resolution"></a>Risoluzione

Non è consigliabile usare i cmdlet AZ e AzureRM nello stesso runbook. Per altre informazioni sull'uso corretto dei moduli, vedere [Eseguire la migrazione a moduli Az](../shared-resources/modules.md#migrate-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenario: accesso negato quando si utilizza una sandbox Azure per un runbook o un'applicazione

### <a name="issue"></a>Problema

Quando viene tentata l'esecuzione del runbook o dell'applicazione in una sandbox di Azure, l'ambiente nega l'accesso.

### <a name="cause"></a>Causa

Questo problema può verificarsi perché le sandbox di Azure impediscono l'accesso a tutti i server COM out-of-process. Un'applicazione o un runbook in modalità sandbox, ad esempio, non può eseguire chiamate né in Strumentazione gestione Windows (WMI) né nel servizio Windows Installer (MSIServer. exe). 

### <a name="resolution"></a>Risoluzione

Per informazioni dettagliate sull'uso delle sandbox di Azure, vedere [Ambiente di esecuzione runbook](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenario: il codice di stato Non consentito non è valido quando si usa Key Vault all'interno di un runbook

### <a name="issue"></a>Problema

Quando si prova ad accedere ad Azure Key Vault tramite un runbook di Automazione di Azure, viene visualizzato l'errore seguente:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Causa

Possibili cause di questo problema sono:

* Mancato uso di un account RunAs.
* Autorizzazioni insufficienti.

### <a name="resolution"></a>Risoluzione

#### <a name="not-using-a-run-as-account"></a>Mancato uso di un account RunAs

Eseguire il passaggio 5 [Aggiungere l'autenticazione per gestire le risorse di Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) per assicurarsi di usare un account RunAs per accedere a Key Vault.

#### <a name="insufficient-permissions"></a>Autorizzazioni insufficienti

[Aggiungere autorizzazioni a Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) per assicurarsi che l'account RunAs disponga di autorizzazioni sufficienti per accedere a Key Vault.

## <a name="recommended-documents"></a>Documenti consigliati

* [Esecuzione di runbook in Automazione di Azure](../automation-runbook-execution.md)
* [Avvio di un Runbook in Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o non è stato possibile risolverlo, usare uno dei canali seguenti per ricevere ulteriore assistenza:

* I [forum di Azure](https://azure.microsoft.com/support/forums/) sono utili per ottenere risposte dagli esperti di Azure.
* L'account ufficiale di Microsoft Azure, [@AzureSupport](https://twitter.com/azuresupport), è ottimizzato per migliorare l'esperienza del cliente. Il supporto tecnico di Azure consente di entrare in contatto con la community di Azure e di ottenere risposte, assistenza e consulenza.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al sito [Supporto tecnico di Azure ](https://azure.microsoft.com/support/options/) e scegliere **Ottenere supporto**.
