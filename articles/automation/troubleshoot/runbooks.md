---
title: Risoluzione dei problemi relativi ai runbook di Automazione di Azure
description: Informazioni su come risolvere i problemi relativi ai runbook di Automazione di Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 532d3d73c939a44678091734f2bbff22267ab6b7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094865"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Risoluzione dei problemi relativi ai runbook

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Errori di autenticazione durante l'utilizzo di runbook di Automazione di Azure

### <a name="sign-in-failed"></a>Scenario: Accesso all'account Azure non riuscito

#### <a name="issue"></a>Problema

Usando i cmdlet `Add-AzureAccount` o `Connect-AzureRmAccount` verrà visualizzato il seguente errore.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Causa

Questo errore si verifica se il nome dell'asset delle credenziali non è valido o se il nome utente e la password utilizzati per impostare l'asset delle credenziali di automazione non sono validi.

#### <a name="resolution"></a>Risoluzione

Per determinare la causa del problema, seguire questa procedura:  

1. Assicurarsi che non siano presenti caratteri speciali, ad esempio il carattere **@** nel nome dell'asset delle credenziali di automazione utilizzato per connettersi ad Azure.  
2. Verificare che sia possibile usare il nome utente e la password archiviati nelle credenziali di Automazione di Azure nell'editor di PowerShell ISE locale. È possibile verificare se il nome utente e la password sono corretti eseguendo i cmdlet seguenti in PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se l'autenticazione non riesce in locale, significa che le credenziali di Azure Active Directory non sono state configurate correttamente. Per ottenere la configurazione corretta dell'account Azure Active Directory, vedere il post di blog relativo all' [autenticazione in Azure con Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) .  

4. In caso di errore temporaneo, provare ad aggiungere la logica di ripetizione alla routine di autenticazione per rendere più affidabile l'autenticazione.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Scenario: Non è possibile trovare la sottoscrizione di Azure

#### <a name="issue"></a>Problema

Usando i cmdlet `Select-AzureSubscription` o `Select-AzureRmSubscription` verrà visualizzato l'errore seguente:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Tipi di errore

Questo errore si verifica se il nome della sottoscrizione non è valido o se l'utente di Azure Active Directory che sta tentando di ottenere i dettagli della sottoscrizione non è configurato come amministratore della sottoscrizione.

#### <a name="resolution"></a>Risoluzione

Per determinare se l'autenticazione in Azure è stata eseguita correttamente e se si ha accesso alla sottoscrizione che si sta tentando di selezionare, seguire questa procedura:  

1. Testare lo script all'esterno di Automazione di Azure per assicurarsi che funzioni autonomamente.
2. Assicurarsi di eseguire il cmdlet **Add-AzureAccount** prima del cmdlet **Select-AzureSubscription**.  
3. Se viene ancora visualizzato questo messaggio di errore, modificare il codice aggiungendo il parametro **-AzureRmContext** dopo il cmdlet **Add-AzureAccount**, quindi eseguire il codice.

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Scenario: L'autenticazione in Azure non è riuscita perché è abilitata l'autenticazione a più fattori

#### <a name="issue"></a>Problema

Durante l'autenticazione in Azure con il nome utente e la password di Azure viene visualizzato l'errore seguente:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Causa

Se nell'account Azure è abilitata l'autenticazione a più fattori, non è possibile usare un utente di Azure Active Directory per l'autenticazione in Azure. È invece necessario usare un certificato o un'entità servizio per l'autenticazione in Azure.

#### <a name="resolution"></a>Risoluzione

Per usare un certificato con i cmdlet del modello di distribuzione classica di Azure, vedere il blog relativo alla [creazione e all'aggiunta di un certificato per la gestione dei servizi Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Per usare un'entità servizio con i cmdlet di Azure Resource Manager, vedere l'argomento relativo alla [creazione di un'entità servizio tramite il portale di Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e quello relativo all'[autenticazione di un'entità servizio con Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Errori comuni durante l'utilizzo di runbook

### <a name="task-was-cancelled"></a>Scenario: il runbook ha esito negativo con errore "Un'attività è stata annullata"

#### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Causa

Questo errore può essere causato dall'utilizzo di moduli di Azure non aggiornati.

#### <a name="resolution"></a>Risoluzione

Questo errore può essere risolto aggiornando i moduli di Azure alla versione più recente.

Nell'account di Automazione fare clic su **Moduli** e quindi su **Aggiorna moduli di Azure**. L'aggiornamento richiede circa 15 minuti; al termine, eseguire di nuovo il runbook con esito negativo. Per ulteriori informazioni sull'aggiornamento dei moduli, vedere [Aggiornare i moduli di Azure in Automazione di Azure](../automation-update-azure-modules.md).

### <a name="child-runbook-auth-failure"></a>Scenario: il runbook figlio non ha esito positivo quando si gestiscono più sottoscrizioni

#### <a name="issue"></a>Problema

Durante l'esecuzione del runbook figlio con `Start-AzureRmRunbook`, il runbook figlio non riesce a gestire le risorse di Azure.

#### <a name="cause"></a>Causa

Il runbook figlio non usa il contesto corretto durante l'esecuzione.

#### <a name="resolution"></a>Risoluzione

Quando si lavora con più sottoscrizioni il contesto della sottoscrizione potrebbe andare perso quando si richiama il runbook figlio. Per garantire che il contesto della sottoscrizione venga passato ai runbook figlio, aggiungere il parametro `AzureRmContext` al cmdlet e passargli il contesto.

```azurepowershell-interactive
# Connect to Azure with RunAs account
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

### <a name="not-recognized-as-cmdlet"></a>Scenario: il runbook ha esito negativo a causa di un cmdlet mancano

#### <a name="issue"></a>Problema

Il runbook ha esito negativo con un errore simile all'esempio seguente:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Causa

Questo errore può dipendere dalle cause seguenti:

1. Il modulo che contiene il cmdlet non viene importato nell'account di automazione
2. Il modulo che contiene il cmdlet viene importato, ma non è aggiornato

#### <a name="resolution"></a>Risoluzione

Questo errore può essere risolto eseguendo una delle attività seguenti:

Se il modulo è un modulo di Azure, vedere [Come aggiornare i moduli di Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md) per informazioni su come aggiornare i moduli nell'account di automazione.

Se è un modulo separato, assicurarsi che il modulo venga importato nell'account di automazione.

### <a name="job-attempted-3-times"></a>Scenario: l'avvio del processo runbook è stato tentato per tre volte, ma non è mai riuscito

#### <a name="issue"></a>Problema

Il runbook ha esito negativo con errore:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Causa

Questo errore può dipendere dalle cause seguenti:

1. Limite di memoria. Esistono limiti per la quantità di memoria allocata a un sandbox con [limiti del servizio di automazione](../../azure-subscription-service-limits.md#automation-limits), pertanto un processo potrebbe non riuscire se usa più di 400 MB di memoria.

1. Socket di rete. Le sandbox di Azure sono limitate a 1000 socket di rete simultanei, come descritto in [Limiti del servizio Automazione](../../azure-subscription-service-limits.md#automation-limits).

1. Modulo incompatibile. Questo errore può verificarsi se le dipendenze del modulo non sono corrette; se non lo sono il runbook restituisce in genere il messaggio "Comando non trovato" o "Non è possibile associare il parametro".

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Metodi consigliati per operare entro il limite di memoria sono, ad esempio, dividere il carico di lavoro tra diversi runbook, non elaborare tutti i dati in memoria, non scrivere output non necessari dai runbook oppure tenere in considerazione il numero di checkpoint scritti nei runbook del flusso di lavoro PowerShell. È possibile usare il metodo Clear, ad esempio `$myVar.clear()` per cancellare la variabile e usare `[GC]::Collect()` per eseguire immediatamente Garbage Collection, in modo da ridurre il footprint della memoria durante l'esecuzione dei runbook.

* Aggiornare i moduli di Azure eseguendo i passaggi [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).  

* Un’altra soluzione consiste nell'eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md). I ruoli di lavoro ibridi non hanno i limiti di memoria e rete che sono invece applicati ai sandbox di Azure.

### <a name="fails-deserialized-object"></a>Scenario: Runbook con esito negativo a causa di un oggetto deserializzato

#### <a name="issue"></a>Problema

Il runbook ha esito negativo con errore:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Causa

Se il runbook è un flusso di lavoro di PowerShell, archivia gli oggetti complessi in un formato deserializzato per rendere persistente lo stato del runbook quando il flusso di lavoro viene sospeso.

#### <a name="resolution"></a>Risoluzione

Una qualsiasi delle tre soluzioni seguenti consente di correggere questo problema:

1. Se si inviano tramite pipe oggetti complessi da un cmdlet a un altro, eseguire il wrapping dei cmdlet in un InlineScript.
2. Passare il nome o il valore necessario dall'oggetto complesso invece di passare l'intero oggetto.
3. Usare un runbook di PowerShell invece di un runbook del flusso di lavoro PowerShell.

### <a name="quota-exceeded"></a>Scenario: Processo del Runbook non riuscito per il superamento della quota allocata

#### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando l'esecuzione del processo supera la quota disponibile di 500 minuti per l'account. Questa quota si applica a tutti i tipi di attività di esecuzione del processo, ad esempio il test di un processo, l'avvio di un processo dal portale, l'esecuzione di un processo con webhook e la pianificazione di un processo da eseguire tramite il portale di Azure o nel proprio data center. Per altre informazioni sui prezzi relativi all'automazione, vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Risoluzione

Se si vogliono usare più di 500 minuti di elaborazione al mese, si deve modificare la sottoscrizione dal livello gratuito al livello Basic. È possibile eseguire l'aggiornamento al livello Basic seguendo questa procedura.  

1. Accedere alla sottoscrizione di Azure.  
2. Selezionare l'account di automazione che si vuole aggiornare.  
3. Fare clic su **Impostazioni** > **Prezzi**.
4. Fare clic su **Abilita** nella parte inferiore della pagina per aggiornare l'account al livello **Basic**.

### <a name="cmdlet-not-recognized"></a>Scenario: Cmdlet non riconosciuto durante l'esecuzione di un runbook

#### <a name="issue"></a>Problema

Il processo del runbook ha esito negativo con errore:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando il motore di PowerShell non trova il cmdlet utilizzato nel runbook. È possibile che il modulo che contiene il cmdlet non sia incluso nell'account, che esista un conflitto di nome con il nome di un runbook o che il cmdlet sia presente anche in un altro modulo e che Automazione non possa risolvere il nome.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:  

* Verificare di aver immesso correttamente il nome del cmdlet.  
* Assicurarsi che il cmdlet esista nell'account di automazione e che non siano presenti conflitti. Per verificare se il cmdlet è presente, aprire un runbook in modalità di modifica e cercare il cmdlet nella libreria o eseguire `Get-Command <CommandName>`. Dopo aver verificato che il cmdlet è disponibile per l'account e che non ci sono conflitti di nomi con altri cmdlet o runbook, aggiungerlo all'area di disegno e assicurarsi di usare un set di parametri valido nel runbook.  
* Nel caso di un conflitto di nomi e se il cmdlet è disponibile in due moduli diversi, è possibile risolvere questo problema usando il nome completo per il cmdlet. Ad esempio, usare **ModuleName\CmdletName**.  
* Se si eseguono i runbook in locale in un gruppo di lavoro ibrido, verificare che il modulo e il cmdlet siano installati nel computer che ospita il ruolo di lavoro ibrido.

### <a name="long-running-runbook"></a>Scenario: un runbook a esecuzione prolungata non viene completato

#### <a name="issue"></a>Problema

Il runbook mostra lo stato **Stopped** (Arrestato) dopo 3 ore di esecuzione. Potrebbe essere visualizzato anche l'errore:

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Si tratta di un comportamento previsto nei sandbox di Azure, causato dalla "condivisione equa" del monitoraggio dei processi in Automazione di Azure, che prevede l'arresto automatico di un runbook se viene eseguito per più di tre ore. Lo stato di un runbook che supera il limite di tempo di condivisione equa è diverso in base al tipo di runbook. I runbook PowerShell e Python sono impostati sullo stato **Stopped** (Arrestato). I runbook PowerShell del flusso di lavoro sono impostati su **Failed** (Non riuscito).

#### <a name="cause"></a>Causa

Il runbook ha superato il limite di esecuzione di 3 ore consentito dalla condivisione equa in un sandbox di Azure.

#### <a name="resolution"></a>Risoluzione

Una soluzione consigliata consiste nell'eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation-hrw-run-runbooks.md).

I ruoli di lavoro ibridi non sono limitati dal massimo di 3 ore del runbook per la [condivisione equa](../automation-runbook-execution.md#fair-share) rispetto a come vengono eseguiti nelle sandbox di Azure. Anche se i ruoli di lavoro ibridi per runbook non sono soggetti al limite di 3 ore della condivisione equa, i runbook eseguiti in ruoli di lavoro ibridi per runbook dovranno tuttavia essere sviluppati per poter supportare i comportamenti di riavvio causati da problemi imprevisti dell'infrastruttura locale.

Un'altra opzione consiste nell'ottimizzare il runbook creando [runbook figlio](../automation-child-runbooks.md). Se il runbook esegue in ciclo la stessa funzione in più risorse, ad esempio un'operazione di database su più database, è possibile spostare tale funzione in un runbook figlio. Ognuno di questi runbook figli verrà eseguito in parallelo in processi separati, riducendo la quantità totale di tempo del runbook padre richiesta per il completamento.

I cmdlet di PowerShell che consentono lo scenario del runbook figlio sono:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) che consente di avviare un runbook e passargli i parametri

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) che consente di controllare lo stato del processo per ogni figlio, se ci sono operazioni che devono essere eseguite al termine del runbook figlio.

## <a name="common-errors-when-importing-modules"></a>Errori comuni durante l'importazione di moduli

### <a name="module-fails-to-import"></a>Scenario: L'importazione del modulo non riesce o i cmdlet non possono essere eseguiti dopo l'importazione

#### <a name="issue"></a>Problema

L'importazione di un modulo ha esito negativo oppure riesce ma non viene estratto alcun cmdlet.

#### <a name="cause"></a>Causa

Di seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure:

* La struttura non corrisponde a quella in cui Automazione dovrebbe trovarsi.
* Il modulo è dipendente da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il cmdlet `New-AzureRmAutomationModule` viene usato per caricare il modulo e non è stato specificato il percorso di archiviazione completo oppure il modulo non è stato caricato con un URL accessibile pubblicamente.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Assicurarsi che il modulo sia conforme al formato seguente: NomeModulo.Zip **->** NomeModulo o NumeroVersione **->** (NomeModulo.psm1, NomeModulo.psd1)
* Aprire il file con estensione psd1 e vedere se il modulo include dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Assicurarsi che le eventuali DLL a cui viene fatto riferimento siano presenti nella cartella del modulo.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.