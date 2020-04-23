---
title: Risoluzione degli errori del Runbook di automazione di Azure
description: Informazioni su come risolvere e risolvere i problemi che possono verificarsi con manuali operativi di automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 73f79145f63e0d8afee7596f1f8231a054ef1c2e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097694"
---
# <a name="troubleshoot-runbook-errors"></a>Risolvere gli errori di Runbook

 Questo articolo descrive i vari errori Runbook che possono verificarsi e come risolverli.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Diagnosi dei problemi di Runbook

Quando si ricevono errori durante l'esecuzione di Runbook in automazione di Azure, è possibile seguire questa procedura per diagnosticare i problemi.

1. **Assicurarsi che lo script Runbook venga eseguito correttamente nel computer locale.** 

    Vedere la [documentazione di PowerShell](/powershell/scripting/overview) o i [documenti Python](https://docs.python.org/3/) per informazioni di riferimento sul linguaggio e moduli di formazione. Eseguendo lo script in locale è possibile individuare e risolvere gli errori comuni, ad esempio:

      * Moduli mancanti
      * Errori di sintassi
      * Errori di logica

2. **Esaminare i [flussi di errore](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)Runbook.**

    Esaminare questi flussi per i messaggi specifici e confrontarli con gli errori documentati in questo articolo.

3. **Verificare che i nodi e l'area di lavoro di automazione dispongano dei moduli richiesti.** 

    Se il Runbook importa i moduli, verificare che siano disponibili per l'account di automazione usando i passaggi elencati in [importare i moduli](../shared-resources/modules.md#importing-modules). Aggiornare i moduli alla versione più recente seguendo le istruzioni riportate in [aggiornare i moduli di Azure in automazione di Azure](..//automation-update-azure-modules.md). Per ulteriori informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi](shared-resources.md#modules)relativi ai moduli.

4. **Eseguire se il Runbook è sospeso o ha esito negativo.**

    * [Controllare gli Stati dei processi](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) definisce gli Stati Runbook e alcune possibili cause.
    * [Aggiungere un output aggiuntivo](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) a Runbook per identificare cosa accade prima della sospensione del Runbook.
    * [Gestire tutte le eccezioni](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) generate dal processo.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: eseguire login-AzureRMAccount per accedere

### <a name="issue"></a>Problema

Quando si esegue un Runbook, viene visualizzato l'errore seguente:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi quando non si utilizza un account RunAs o l'account RunAs è scaduto. Vedere [gestire gli account RunAs di automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Questo errore ha due cause principali:

* Sono disponibili versioni diverse del modulo AzureRM o AZ.
* Si sta provando ad accedere alle risorse in una sottoscrizione separata.

### <a name="resolution"></a>Risoluzione

Se questo errore viene visualizzato dopo l'aggiornamento di un modulo AzureRM o AZ, è necessario aggiornare tutti i moduli alla stessa versione.

Se si sta provando ad accedere alle risorse in un'altra sottoscrizione, è possibile seguire questa procedura per configurare le autorizzazioni.

1. Passare all'account RunAs di automazione e copiare l'ID applicazione e l'identificazione personale.

    ![ID copia e identificazione personale](../media/troubleshoot-runbooks/collect-app-id.png)

1. Passare al controllo di accesso della sottoscrizione in cui l'account di automazione non è ospitato e aggiungere una nuova assegnazione di ruolo.

    ![Controllo di accesso](../media/troubleshoot-runbooks/access-control.png)

1. Aggiungere l'ID applicazione raccolto in precedenza. Selezionare autorizzazioni Collaboratore.

    ![Aggiungi un'assegnazione di ruolo](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Copiare il nome della sottoscrizione.

1. È ora possibile usare il codice Runbook seguente per testare le autorizzazioni dall'account di automazione all'altra sottoscrizione. Sostituire `"\<CertificateThumbprint\>"` con il valore copiato nel passaggio 1. Sostituire `"\<SubscriptionName\>"` con il valore copiato nel passaggio 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: Non è possibile trovare la sottoscrizione di Azure

### <a name="issue"></a>Problema

Quando si utilizza il `Select-AzureSubscription`cmdlet, o `Select-AzureRMSubscription` `Select-AzSubscription` , viene visualizzato l'errore seguente:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Errore

Questo errore si verifica nei seguenti casi:

* Il nome della sottoscrizione non è valido.
* Il Azure Active Directory utente che sta tentando di ottenere i dettagli della sottoscrizione non è configurato come amministratore della sottoscrizione.
* Il cmdlet non è disponibile.

### <a name="resolution"></a>Risoluzione

Attenersi alla procedura seguente per determinare se è stata eseguita l'autenticazione in Azure e si ha accesso alla sottoscrizione che si sta tentando di selezionare.

1. Per assicurarsi che lo script funzioni autonomo, testarlo all'esterno di automazione di Azure.
2. Assicurarsi che lo script esegua il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) prima di eseguire `Select-*` il cmdlet.
3. Aggiungere `Disable-AzContextAutosave –Scope Process` all'inizio del runbook. Questa chiamata al cmdlet garantisce che tutte le credenziali si applichino solo all'esecuzione del Runbook corrente.
4. Se questo messaggio di errore viene ancora visualizzato, modificare il codice aggiungendo il `AzContext` parametro per `Connect-AzAccount`, quindi eseguire il codice.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
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

Per usare un certificato con i cmdlet del modello di distribuzione classica di Azure, vedere la pagina relativa alla [creazione e all'aggiunta di un certificato per gestire i servizi di Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Per usare un'entità servizio con i cmdlet di Azure Resource Manager, vedere Creazione di un' [entità servizio mediante portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [autenticazione di un'entità servizio con Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: viene visualizzato un errore nei flussi del processo relativi al metodo get_SerializationSettings

### <a name="issue"></a>Problema

Viene visualizzato l'errore seguente nei flussi del processo per un Runbook:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Causa

Questo errore è causato dall'uso dei cmdlet AzureRM e AZ Module in un Runbook. Si verifica quando si importa il modulo AZ prima di importare il modulo AzureRM.

### <a name="resolution"></a>Risoluzione

I cmdlet AZ e AzureRM non possono essere importati e usati nello stesso Runbook. Per altre informazioni sui cmdlet AZ in automazione di Azure, vedere [gestire i moduli in automazione di Azure](../shared-resources/modules.md).

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

1. Nell'account di automazione fare clic su **moduli**, quindi **aggiornare i moduli di Azure**. 
2. L'aggiornamento richiede circa 15 minuti. Al termine, eseguire di nuovo il Runbook che ha avuto esito negativo.

Per altre informazioni sull'aggiornamento dei moduli, vedere [aggiornare i moduli di Azure in automazione di Azure](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: manuali operativi ha esito negativo quando si gestiscono più sottoscrizioni

### <a name="issue"></a>Problema

Quando si esegue manuali operativi, Runbook non riesce a gestire le risorse di Azure.

### <a name="cause"></a>Causa

Il runbook non usa il contesto corretto durante l'esecuzione.

### <a name="resolution"></a>Risoluzione

È possibile che il contesto della sottoscrizione vada perso quando un Runbook richiama più manuali operativi. Per assicurarsi che il contesto della sottoscrizione venga passato a manuali operativi, fare in modo che il client Runbook passi il `Start-AzureRmAutomationRunbook` contesto al cmdlet `AzureRmContext` nel parametro. Utilizzare il `Disable-AzureRmContextAutosave` cmdlet con il `Scope` parametro impostato su `Process` per assicurarsi che le credenziali specificate vengano utilizzate solo per il Runbook corrente. Per ulteriori informazioni, vedere [utilizzo di più sottoscrizioni](../automation-runbook-execution.md#working-with-multiple-subscriptions).

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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: termine non riconosciuto come nome di cmdlet, funzione, script

### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Questo errore può verificarsi per i motivi seguenti:

* Il modulo che contiene il cmdlet non viene importato nell'account di automazione.
* Il modulo che contiene il cmdlet viene importato ma non è aggiornato.

### <a name="resolution"></a>Risoluzione

Per risolvere l'errore, eseguire una delle attività seguenti. 

* Per un modulo di Azure, vedere [How to update Azure PowerShell Modules in automazione di Azure](../automation-update-azure-modules.md) per informazioni su come aggiornare i moduli nell'account di automazione.

* Per un modulo non di Azure, assicurarsi che il modulo sia stato importato nell'account di automazione.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: l'avvio del processo Runbook è stato tentato tre volte, ma non è stato possibile avviarsi ogni volta

### <a name="issue"></a>Problema

Il Runbook ha esito negativo con l'errore seguente:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Questo errore si verifica a causa di uno dei problemi seguenti:

* Limite di memoria. Un processo potrebbe non riuscire se usa più di 400 MB di memoria. I limiti documentati sulla memoria allocata a una sandbox si trovano ai [limiti del servizio di automazione](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Socket di rete. Azure sandbox è limitato a 1000 socket di rete simultanei. Vedere [limiti del servizio di automazione](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modulo incompatibile. Le dipendenze del modulo potrebbero non essere corrette. In questo caso, il Runbook restituisce in genere `Command not found` un `Cannot bind parameter` messaggio o.

* Nessuna autenticazione con Active Directory per sandbox. Il Runbook ha tentato di chiamare un eseguibile o un sottoprocesso eseguito in una sandbox di Azure. La configurazione di manuali operativi per l'autenticazione con Azure AD tramite la libreria di autenticazione Azure Active Directory (ADAL) non è supportata.

* Troppa quantità di dati di eccezione. Il Runbook ha tentato di scrivere una quantità eccessiva di dati di eccezione nel flusso di output.

### <a name="resolution"></a>Risoluzione

* Limite di memoria, socket di rete. I modi consigliati per lavorare entro i limiti di memoria sono suddividere il carico di lavoro tra più manuali operativi, elaborare meno dati in memoria, evitare di scrivere output superfluo dalla manuali operativi e prendere in considerazione il numero di checkpoint scritti nel manuali operativi del flusso di lavoro PowerShell. Usare il metodo Clear, ad esempio `$myVar.clear`, per cancellare le variabili e usare `[GC]::Collect` per eseguire immediatamente Garbage Collection. in modo da ridurre il footprint della memoria durante l'esecuzione dei runbook.

* Modulo incompatibile. Aggiornare i moduli di Azure seguendo i passaggi descritti in [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

* Nessuna autenticazione con Active Directory per sandbox. Quando si esegue l'autenticazione per Azure AD con una Runbook, assicurarsi che il modulo di Azure AD sia disponibile nell'account di automazione. Assicurarsi di concedere all'account RunAs le autorizzazioni necessarie per eseguire le attività automatizzate da Runbook.

  Se il Runbook non può chiamare un eseguibile o un sottoprocesso in esecuzione in una sandbox di Azure, usare Runbook in un ruolo di [lavoro ibrido per Runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non sono limitati dai limiti di memoria e di rete di Azure sandbox.

* Troppa quantità di dati di eccezione. È previsto un limite di 1 MB nel flusso di output del processo. Assicurarsi che Runbook racchiuda le chiamate a un eseguibile o a un `try` sottoprocesso `catch` usando i blocchi e. Se le operazioni generano un'eccezione, fare in modo che il codice scriva il messaggio dall'eccezione in una variabile di automazione. Questa tecnica impedisce che il messaggio venga scritto nel flusso di output del processo.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: accesso all'account di Azure non riuscito

### <a name="issue"></a>Problema

Quando si utilizza il `Connect-AzAccount` cmdlet, viene visualizzato uno degli errori seguenti:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Questi errori si verificano se il nome dell'asset delle credenziali non è valido. Potrebbero verificarsi anche se il nome utente e la password usati per impostare l'asset delle credenziali di automazione non sono validi.

### <a name="resolution"></a>Risoluzione

Per determinare la causa del problema, seguire questa procedura:

1. Assicurarsi che non siano presenti caratteri speciali. ad esempio il carattere `\@` nel nome dell'asset delle credenziali di automazione usato per connettersi ad Azure.
2. Verificare se è possibile usare il nome utente e la password archiviati nelle credenziali di automazione di Azure nell'editor di PowerShell ISE locale. Eseguire i cmdlet seguenti in PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Se l'autenticazione non riesce in locale, non sono state configurate correttamente le credenziali Azure Active Directory. Per ottenere la configurazione corretta dell'account Azure Active Directory, vedere il post di Blog relativo all' [autenticazione in Azure con Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) .

4. Se l'errore sembra essere temporaneo, provare ad aggiungere la logica di ripetizione dei tentativi alla routine di autenticazione per rendere l'autenticazione più affidabile.

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

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: riferimento a un oggetto non corretto nella chiamata a Add-AzAccount

### <a name="issue"></a>Problema

Questo errore viene visualizzato quando si utilizza `Add-AzAccount`, che è un alias per il `Connect-AzAccount` cmdlet:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Questo errore può verificarsi se Runbook non esegue i passaggi appropriati prima di chiamare `Add-AzAccount` per aggiungere l'account di automazione. Un esempio di uno dei passaggi necessari è l'accesso con un account RunAs. Per le operazioni corrette da usare in Runbook, vedere [esecuzione di Runbook in automazione di Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: riferimento all'oggetto non impostato su un'istanza di un oggetto

### <a name="issue"></a>Problema

Viene visualizzato l'errore seguente quando si richiama un Runbook figlio con il `Wait` parametro e il flusso di output contiene un oggetto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

`Start-AzAutomationRunbook`non gestisce correttamente il flusso di output se il flusso contiene oggetti.

### <a name="resolution"></a>Risoluzione

È consigliabile implementare una logica di polling e usare il cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) per recuperare l'output. Di seguito è riportato un esempio di questa logica.

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

Per risolvere il problema, usare una delle soluzioni seguenti.

* Se si inviano tramite pipe oggetti complessi da un cmdlet a un altro, eseguire il wrapping `InlineScript` di questi cmdlet in un'attività.
* Passare il nome o il valore necessario dall'oggetto complesso invece di passare l'intero oggetto.
* Usare un runbook di PowerShell invece di un runbook del flusso di lavoro PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scenario: il processo Runbook non riesce perché è stata superata la quota allocata

### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Questo errore si verifica quando l'esecuzione del processo supera la quota disponibile di 500 minuti per l'account. Questa quota si applica a tutti i tipi di attività di esecuzione di processi, Alcune di queste attività testano un processo, avviando un processo dal portale, eseguendo un processo usando webhook o pianificando un processo da eseguire usando il portale di Azure o il Data Center. Per altre informazioni sui prezzi relativi all'automazione, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Risoluzione

Se si vogliono usare più di 500 minuti di elaborazione al mese, modificare la sottoscrizione dal livello gratuito al livello Basic.

1. Accedere alla sottoscrizione di Azure.
2. Selezionare l'account di automazione da aggiornare.
3. Fare clic su **Impostazioni**, quindi su **prezzi**.
4. Fare clic su **Abilita** nella parte inferiore della pagina per aggiornare l'account al livello Basic.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: Cmdlet non riconosciuto durante l'esecuzione di un runbook

### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando il motore di PowerShell non trova il cmdlet utilizzato nel runbook. È possibile che il modulo che contiene il cmdlet non sia presente nell'account, che si verifichi un conflitto di nomi con un nome Runbook o che il cmdlet esista anche in un altro modulo e che l'automazione non possa risolvere il nome.

### <a name="resolution"></a>Risoluzione

Per risolvere il problema, usare una delle soluzioni seguenti.

* Assicurarsi di aver immesso correttamente il nome del cmdlet.
* Verificare che il cmdlet esista nell'account di automazione e che non siano presenti conflitti. Per verificare se il cmdlet è presente, aprire un Runbook in modalità di modifica e cercare il cmdlet che si desidera trovare nella libreria oppure eseguire `Get-Command <CommandName>`. Dopo aver convalidato che il cmdlet è disponibile per l'account e che non sono presenti conflitti di nome con altri cmdlet o manuali operativi, aggiungere il cmdlet all'area di disegno e assicurarsi di usare un set di parametri valido in Runbook.
* Se si verifica un conflitto di nomi e il cmdlet è disponibile in due moduli diversi, risolvere il problema usando il nome completo del cmdlet. È ad esempio possibile usare `ModuleName\CmdletName`.
* Se si esegue Runbook in locale in un gruppo di lavoro ibrido, verificare che il modulo e il cmdlet siano installati nel computer che ospita il ruolo di lavoro ibrido.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: non è possibile completare un Runbook a esecuzione prolungata

### <a name="issue"></a>Problema

Il runbook mostra lo stato Stopped (Arrestato) dopo 3 ore di esecuzione. È anche possibile che venga visualizzato questo errore:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Questo comportamento è progettato in sandbox di Azure a causa del monitoraggio della [condivisione equa](../automation-runbook-execution.md#fair-share) dei processi in automazione di Azure. Se un processo viene eseguito più di tre ore, la condivisione equa interrompe automaticamente un Runbook. Lo stato di un Runbook che supera il limite di tempo della condivisione equa è diverso per il tipo Runbook. I runbook PowerShell e Python sono impostati sullo stato Stopped (Arrestato). I runbook PowerShell del flusso di lavoro sono impostati su Failed (Non riuscito).

### <a name="cause"></a>Causa

Il Runbook è stato eseguito nel limite di 3 ore consentito dalla condivisione equa in una sandbox di Azure.

### <a name="resolution"></a>Risoluzione

Una soluzione consigliata consiste nell'eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non sono limitati entro il limite di 3 ore per la condivisione equa Runbook di Azure sandbox. I manuali operativi eseguiti sui ruoli di lavoro ibridi per Runbook devono essere sviluppati per supportare i comportamenti di riavvio in caso di problemi di infrastruttura locale imprevisti.

Un'altra soluzione consiste nell'ottimizzare il Runbook creando [manuali operativi figlio](../automation-child-runbooks.md). Se il Runbook esegue il ciclo della stessa funzione su più risorse, ad esempio in un'operazione di database su diversi database, è possibile spostare la funzione in un Runbook figlio. Ogni Runbook figlio viene eseguito in parallelo in un processo separato. Questo comportamento riduce la quantità totale di tempo per il completamento del runbook padre.

I cmdlet di PowerShell che consentono lo scenario del runbook figlio sono:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). questo cmdlet consente di avviare un runbook e trasmettergli i parametri.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Se sono presenti operazioni che devono essere eseguite dopo il completamento del Runbook figlio, questo cmdlet consente di controllare lo stato del processo per ogni elemento figlio.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: stato: 400 richiesta non valida quando si chiama un webhook

### <a name="issue"></a>Problema

Quando si tenta di richiamare un webhook per un Runbook di automazione di Azure, viene visualizzato l'errore seguente:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

Il webhook che si sta tentando di chiamare è disabilitato o scaduto.

### <a name="resolution"></a>Risoluzione

Se il webhook è disattivato, è possibile riabilitarlo tramite il portale di Azure. Se il webhook è scaduto, è necessario eliminarlo e ricrearlo. Il [rinnovo di un webhook](../automation-webhooks.md#renew-webhook) è possibile solo se il webhook non è scaduto.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: frequenza delle richieste troppo grande...

### <a name="issue"></a>Problema

Quando si esegue il cmdlet `Get-AzAutomationJobOutput` viene visualizzato il messaggio di errore seguente:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Questo errore può verificarsi durante il recupero dell'output del processo da un runbook con molti [flussi dettagliati](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Risoluzione

Per risolvere l'errore, eseguire una delle operazioni seguenti.

* Modificare il runbook riducendo il numero di flussi del processo generati dal runbook stesso.

* Ridurre il numero di flussi da recuperare all'esecuzione del cmdlet. A tale scopo, è possibile impostare il valore del `Stream` parametro per il cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) per recuperare solo i flussi di output. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scenario: il processo di PowerShell non riesce con errore: Impossibile richiamare il metodo

### <a name="issue"></a>Problema

Quando si avvia un processo di PowerShell in un Runbook in esecuzione in Azure, viene visualizzato il messaggio di errore seguente:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Questo errore potrebbe indicare che l'esecuzione di manuali operativi in una sandbox di Azure non può essere eseguita in [modalità linguaggio completo](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Risoluzione

Esistono due modi per risolvere l'errore.

* Invece di usare [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7), usare [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) per avviare il Runbook.
* Provare a eseguire Runbook in un ruolo di lavoro ibrido per Runbook.

Per altre informazioni su questo comportamento e altri comportamenti dei manuali operativi di automazione di Azure, vedere [comportamento di Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scenario: il ruolo di lavoro ibrido per Runbook di Linux riceve una richiesta di password quando firma un Runbook

### <a name="issue"></a>Problema

L'esecuzione `sudo` del comando per un ruolo di lavoro ibrido per Runbook di Linux recupera una richiesta di password imprevista.

### <a name="cause"></a>Causa

L'account **nxautomationuser** per l'agente di log Analytics per Linux non è configurato correttamente nel file **sudoers** . Il ruolo di lavoro ibrido per Runbook richiede la configurazione appropriata delle autorizzazioni dell'account e di altri dati, in modo che possa firmare manuali operativi nel ruolo di lavoro Runbook di Linux.

### <a name="resolution"></a>Risoluzione

* Verificare che il ruolo di lavoro ibrido per Runbook includa il file eseguibile di GnuPG (GPG) nel computer.

* Verificare la configurazione dell'account **nxautomationuser** nel file **sudoers** . Vedere [esecuzione di manuali operativi in un ruolo di lavoro ibrido per Runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: errore del cmdlet in Runbook di PowerShell PnP in automazione di Azure

### <a name="issue"></a>Problema

Quando un Runbook scrive direttamente un oggetto generato da PowerShell PnP nell'output di automazione di Azure, l'output del cmdlet non riesce a eseguire il flusso all'automazione.

### <a name="cause"></a>Causa

Questo problema si verifica in genere quando automazione di Azure elabora manuali operativi che richiamano i cmdlet di PowerShell `add-pnplistitem`PNP, ad esempio, senza intercettare gli oggetti restituiti.

### <a name="resolution"></a>Risoluzione

Modificare gli script per assegnare i valori restituiti alle variabili in modo che i cmdlet non tentino di scrivere interi oggetti nell'output standard. Uno script può reindirizzare il flusso di output a un cmdlet come illustrato di seguito.

```azurecli
  $null = add-pnplistitem
```

Se lo script analizza l'output del cmdlet, lo script deve archiviare l'output in una variabile e modificare la variabile anziché semplicemente trasmettere l'output.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Scenario: codice di stato non valido "Forbidden" quando si usa Key Vault all'interno di un Runbook

### <a name="issue"></a>Problema

Quando si prova ad accedere a Key Vault tramite un Runbook di automazione di Azure, si verifica l'errore seguente:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Causa

Possibili cause del problema:

* Non usare un account RunAs.
* Autorizzazioni insufficienti.

### <a name="resolution"></a>Risoluzione

#### <a name="not-using-run-as-account"></a>Non uso dell'account RunAs

Eseguire i passaggi descritti in [passaggio 5: aggiungere l'autenticazione per gestire le risorse di Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) per assicurarsi di usare un account RunAs per accedere a Key Vault. 

#### <a name="insufficient-permissions"></a>Autorizzazioni insufficienti

Per assicurarsi che l'account RunAs disponga di autorizzazioni sufficienti per accedere a Key Vault, attenersi alla procedura descritta in [aggiungere autorizzazioni a Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) . 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>il problema non è incluso in questo elenco

Le sezioni seguenti elencano altri errori comuni e forniscono la documentazione di supporto per aiutare a risolvere il problema.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Il ruolo di lavoro per runbook ibrido non esegue i processi o non risponde

Se si eseguono processi in un ruolo di lavoro ibrido per Runbook invece che in automazione di Azure, potrebbe essere necessario [risolvere i problemi del ruolo di lavoro ibrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook ha esito negativo senza autorizzazione o alcune variazioni

Gli account RunAs potrebbero non avere le stesse autorizzazioni per le risorse di Azure dell'account corrente. Verificare che l'account RunAs disponga [delle autorizzazioni per accedere a tutte le risorse](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilizzate nello script.

### <a name="issues-passing-parameters-into-webhooks"></a>Problemi durante il passaggio dei parametri ai webhook

Per informazioni sul passaggio di parametri ai webhook, vedere [avviare un Runbook da un webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problemi con AZ Modules

Non è supportato l'uso dei moduli AZ modules e AzureRM nello stesso account di automazione. Per altri dettagli, vedere [AZ Modules in manuali operativi](https://docs.microsoft.com/azure/automation/az-modules) .

### <a name="inconsistent-behavior-in-runbooks"></a>Comportamento incoerente nei runbook

Seguire le linee guida nell' [esecuzione di Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) per evitare problemi con i processi simultanei, le risorse create più volte o altre logiche sensibili alla temporizzazione in manuali operativi.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook ha esito negativo con errore, nessuna autorizzazione, non consentito (403) o alcune variazioni

Gli account RunAs potrebbero non avere le stesse autorizzazioni per le risorse di Azure dell'account corrente. Verificare che l'account RunAs disponga [delle autorizzazioni per accedere a tutte le risorse](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilizzate nello script.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>I runbook funzionavano ma sono stati arrestati improvvisamente

* Verificare che l'account RunAs non sia scaduto. Vedere [rinnovo della certificazione](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se si usa un [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) per avviare un Runbook, assicurarsi che il webhook non sia scaduto.

### <a name="passing-parameters-into-webhooks"></a>Trasmissione dei parametri nei webhook

Per informazioni sul passaggio di parametri ai webhook, vedere [avviare un Runbook da un webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Uso dei moduli Az

Non è supportato l'uso dei moduli AZ modules e AzureRM nello stesso account di automazione. Vedere [AZ Modules in manuali operativi](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Utilizzo di certificati autofirmati

Per usare i certificati autofirmati, vedere [creazione di un nuovo certificato](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Accesso negato quando si usa sandbox di Azure per un Runbook

Azure sandbox impedisce l'accesso a tutti i server COM out-of-process. Ad esempio, un'applicazione o un Runbook in modalità sandbox non può chiamare Strumentazione gestione Windows (WMI) o nel servizio di Windows Installer (MSIServer. exe). Per informazioni dettagliate sull'uso della sandbox, vedere [esecuzione di Runbook in automazione di Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Documenti consigliati

* [Avvio di un Runbook in automazione di Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Esecuzione di runbook in Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connetti con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.
