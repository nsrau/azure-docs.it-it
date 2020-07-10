---
title: Esecuzione di runbook in Automazione di Azure
description: Questo articolo offre una panoramica dell'elaborazione dei runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6db4ceed0121f072104312ac24abb13fb241737b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186045"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

L'automazione dei processi in Automazione di Azure consente di creare e gestire PowerShell, il flusso di lavoro di PowerShell e i runbook grafici. Per informazioni dettagliate, vedere [Runbook di Automazione di Azure](automation-runbook-types.md). 

Automazione esegue i runbook in base alla logica definita al loro interno. Se un runbook viene interrotto, si riavvia dall'inizio. Questo comportamento richiede la scrittura di runbook che supportano il riavvio in caso di problemi temporanei.

L'avvio di un runbook in Automazione di Azure crea un processo, ovvero una singola istanza di esecuzione del runbook. Ogni processo accede alle risorse di Azure eseguendo una connessione alla sottoscrizione di Azure. Il processo può accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

Automazione di Azure assegna l'esecuzione di ogni processo durante l'esecuzione del runbook a un ruolo di lavoro. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non è possibile controllare quali servizi del ruolo di lavoro vengono richiesti dal processo.

Quando si visualizza l'elenco di runbook nel portale di Azure, è visibile lo stato di tutti i processi avviati per ogni runbook. Automazione di Azure conserva i log dei processi fino a 30 giorni.

Il diagramma seguente mostra il ciclo di vita di un processo del runbook per i [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks), i [runbook del flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e i [runbook grafici](automation-runbook-types.md#graphical-runbooks).

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Ambiente di esecuzione dei runbook

I runbook in Automazione di Azure possono essere eseguiti in una sandbox di Azure o in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md). 

Quando i runbook vengono progettati per l'autenticazione e l'esecuzione nelle risorse di Azure, vengono eseguiti in una sandbox di Azure, ovvero in un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni di risorse della sandbox. L'ambiente sandbox di Azure non supporta le operazioni interattive. Impedisce l'accesso a tutti i server COM esterni. Richiede anche l'uso di file MOF locali per i runbook che effettuano chiamate Win32.

È anche possibile usare un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) per eseguire i runbook direttamente nel computer che ospita il ruolo e nelle risorse locali dell'ambiente. Automazione di Azure archivia e gestisce i runbook, poi li distribuisce a uno o più computer assegnati.

>[!NOTE]
>Per l'esecuzione in un ruolo di lavoro ibrido per runbook di Linux, gli script devono essere firmati e il ruolo di lavoro deve essere configurato di conseguenza. In alternativa, [è necessario disattivare la convalida della firma](automation-linux-hrw-install.md#turn-off-signature-validation). 

La tabella seguente elenca alcune attività di esecuzione del runbook insieme al relativo ambiente di esecuzione per ognuna di esse.

|Attività|Recommendation|Note|
|---|---|---|
|Integrazione con le risorse di Azure|Sandbox di Azure|Se ospitata in Azure, l'autenticazione è più semplice. Se si usa un ruolo di lavoro ibrido per runbook in una macchina virtuale di Azure, è possibile [usare l'autenticazione del runbook con le identità gestite](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Ottenere prestazioni ottimali per gestire le risorse di Azure|Sandbox di Azure|Lo script viene eseguito nello stesso ambiente, che ha una latenza inferiore.|
|Ridurre al minimo i costi operativi|Sandbox di Azure|Non c'è alcun overhead di calcolo e non è necessaria una macchina virtuale.|
|Eseguire uno script con esecuzione prolungata|ruolo di lavoro ibrido per runbook|Le sandbox di Azure hanno [limiti di risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagire con i servizi locali|ruolo di lavoro ibrido per runbook|Accedere direttamente al computer host oppure alle risorse in altri ambienti cloud o nell'ambiente locale. |
|Richiedere software ed eseguibili di terze parti|ruolo di lavoro ibrido per runbook|L'utente gestisce il sistema operativo e può installare i software.|
|Monitorare un file o una cartella con un runbook|ruolo di lavoro ibrido per runbook|Usare un'[attività watcher](automation-watchers-tutorial.md) in un ruolo di lavoro ibrido per runbook.|
|Eseguire uno script con uso intensivo delle risorse|ruolo di lavoro ibrido per runbook| Le sandbox di Azure hanno [limiti di risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Usare moduli con requisiti specifici| ruolo di lavoro ibrido per runbook|Ad esempio:</br> WinSCP - dipendenza da winscp.exe </br> Amministrazione di IIS - dipendenza dall'abilitazione o dalla gestione di IIS|
|Installare un modulo con un programma di installazione|ruolo di lavoro ibrido per runbook|I moduli per la sandbox devono supportare la copia.|
|Usare runbook o moduli che richiedono una versione di .NET Framework diversa dalla 4.7.2|ruolo di lavoro ibrido per runbook|Le sandbox di Azure supportano .NET Framework 4.7.2 e l'aggiornamento a una versione diversa non è supportato.|
|Eseguire script che richiedono l'elevazione dei privilegi|ruolo di lavoro ibrido per runbook|Le sandbox non consentono l'elevazione dei privilegi. Con un ruolo di lavoro ibrido per runbook è possibile disattivare il Controllo dell'account utente e usare [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) quando si esegue il comando che richiede l'elevazione dei privilegi.|
|Eseguire script che richiedono l'accesso a Strumentazione gestione Windows (WMI)|ruolo di lavoro ibrido per runbook|I processi in esecuzione nelle sandbox del cloud non possono accedere al provider WMI. |

## <a name="resources"></a>Risorse

I runbook devono includere la logica per gestire le [risorse](/rest/api/resources/resources), ad esempio, le macchine virtuali, la rete e le risorse sulla rete. Le risorse sono associate a una sottoscrizione di Azure e i runbook richiedono credenziali appropriate per accedervi. Per un esempio di gestione delle risorse in un runbook, vedere [Gestire risorse](manage-runbooks.md#handle-resources). 

## <a name="security"></a>Sicurezza

Automazione di Azure usa il [Centro sicurezza di Azure](../security-center/security-center-intro.md) per garantire la sicurezza delle risorse e rilevare i compromessi nei sistemi Linux. La sicurezza viene garantita tra i carichi di lavoro, indipendentemente dal fatto che le risorse si trovino in Azure o meno. Vedere [Introduzione all'autenticazione in Automazione di Azure](automation-security-overview.md).

Il Centro sicurezza di Azure pone vincoli agli utenti che possono eseguire qualsiasi script, firmato o meno, in una macchina virtuale. Se si ha accesso alla radice di una macchina virtuale, è necessario configurare in modo esplicito la macchina con una firma digitale oppure disattivarla. Altrimenti, è possibile eseguire solo uno script per applicare gli aggiornamenti del sistema operativo dopo aver creato un account di Automazione e aver abilitato la funzionalità appropriata.

## <a name="subscriptions"></a>Sottoscrizioni

Una [sottoscrizione](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) di Azure è un contratto con Microsoft per l'uso di uno o più servizi basati su cloud, il cui costo viene addebitato. Per Automazione di Azure, ogni sottoscrizione è collegata a un account di Automazione di Azure ed è possibile [creare più sottoscrizioni](manage-runbooks.md#work-with-multiple-subscriptions) nell'account.

## <a name="credentials"></a>Credenziali

Un runbook richiede credenziali [appropriate](shared-resources/credentials.md) per accedere a una risorsa, sia per i sistemi Azure che per quelli di terze parti. Queste credenziali vengono archiviate in Automazione di Azure, Key Vault e così via.  

## <a name="azure-monitor"></a>Monitoraggio di Azure

Automazione di Azure usa [monitoraggio di Azure](../azure-monitor/overview.md) per monitorare le operazioni del computer. Per le operazioni sono necessari un'area di lavoro Log Analytics e gli [agenti di Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Agente di Log Analytics per Windows

L'[agente di Log Analytics per Windows](../azure-monitor/platform/agent-windows.md) funziona con Monitoraggio di Azure per gestire le macchine virtuali Windows e i computer fisici. I computer possono essere eseguiti in Azure o in un ambiente non Azure, ad esempio un data center locale. È necessario configurare l'agente affinché esegua la segnalazione a una o più aree di lavoro Log Analytics. 

>[!NOTE]
>L'agente di Log Analytics per Windows era noto in precedenza come Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agente di Log Analytics per Linux

L'[agente di Log Analytics per Linux](../azure-monitor/platform/agent-linux.md) funziona in modo analogo all'agente per Windows, ma connette i computer Linux a Monitoraggio di Azure. L'agente viene installato con un account utente **nxautomation** che consente l'esecuzione di comandi che richiedono autorizzazioni radice, ad esempio, in un ruolo di lavoro ibrido per runbook. L'account **nxautomation** è un account di sistema che non richiede una password. 

L'account **nxautomation** deve essere presente con le autorizzazioni sudo corrispondenti durante l'[installazione di un ruolo di lavoro ibrido per runbook di Linux](automation-linux-hrw-install.md). Se si tenta di installare il ruolo di lavoro e l'account non è presente o non dispone delle autorizzazioni appropriate, l'installazione non riesce.

I log disponibili per l'agente di Log Analytics e l'account **nxautomation** sono:

* /var/opt/microsoft/omsagent/log/omsagent.log - Log dell'agente di Log Analytics 
* /var/opt/Microsoft/omsagent/run/automationworker/worker.log - Log del ruolo di lavoro di Automazione

>[!NOTE]
>L'utente **nxautomation** abilitato come parte di Gestione aggiornamenti esegue solo runbook firmati.

## <a name="runbook-permissions"></a>Autorizzazioni per i runbook

Il runbook richiede le autorizzazioni per l'autenticazione in Azure tramite le credenziali. Vedere [Gestire account RunAs di Automazione di Azure](manage-runas-account.md). 

## <a name="modules"></a>Moduli

Automazione di Azure supporta una serie di moduli predefiniti, tra cui alcuni moduli AzureRM (AzureRM.Automation) e un modulo che contiene diversi cmdlet interni. Sono inoltre supportati i moduli installabili, tra cui i moduli Az (Az.Automation), attualmente preferiti ai moduli AzureRM. Per informazioni dettagliate sui moduli disponibili per le configurazioni di runbook e DSC, vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).

## <a name="certificates"></a>Certificati

Automazione di Azure usa i [certificati](shared-resources/certificates.md) per l'autenticazione in Azure o li aggiunge alle risorse di Azure o di terze parti. I certificati vengono archiviati in modo sicuro per consentirne l'accesso ai runbook e alle configurazioni DSC. 

I runbook possono usare certificati autofirmati, che non sono firmati da un'autorità di certificazione (CA). Vedere [Creare un nuovo certificato](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Processi

Automazione di Azure supporta un ambiente per l'esecuzione di processi dallo stesso account di Automazione. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox. 

I processi in esecuzione nello stesso processo sandbox possono influire l'uno sull'altro. Un esempio è dato dal cmdlet [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0). L'esecuzione di questo cmdlet disconnette ogni processo runbook nel processo sandbox condiviso. Per un esempio di utilizzo di questo scenario, vedere [Evitare i processi simultanei](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>I processi di PowerShell avviati da un runbook eseguito in una sandbox di Azure potrebbero non essere eseguiti nella [modalità del linguaggio PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes) completa. 

### <a name="job-statuses"></a>Stati dei processi

La tabella seguente descrive gli stati possibili per un processo. È possibile visualizzare un riepilogo dello stato di tutti i processi del runbook o esaminare i dettagli di uno specifico processo del runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con i log di Monitoraggio di Azure, vedere [Inoltrare lo stato e i flussi del processo da Automazione ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md). Per un esempio di utilizzo degli stati in un runbook, vedere anche [Ottenere gli stati del processo](manage-runbooks.md#obtain-job-statuses).

| Stato | Descrizione |
|:--- |:--- |
| Completi |Il processo è stato completato. |
| Operazione non riuscita |La compilazione di un runbook grafico o di un runbook del flusso di lavoro di PowerShell non è riuscita. Non è stato possibile avviare il runbook di PowerShell oppure il processo conteneva un'eccezione. Vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md).|
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Queued |Il processo è in attesa che le risorse diventino disponibili in un ruolo di lavoro di Automazione per poter essere avviato. |
| Resuming |Il sistema sta riprendendo il processo dopo che è stato sospeso. |
| In esecuzione |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di condivisione equa. Riprenderà a breve dall'ultimo checkpoint. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Si applica solo ai [runbook grafici e ai runbook del flusso di lavoro di PowerShell](automation-runbook-types.md). Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Se un runbook non ha un checkpoint, viene avviato dall'inizio. Se ha un checkpoint, può essere nuovamente avviato e riprendere dall'ultimo checkpoint. Il runbook viene sospeso dal sistema solo quando si verifica un'eccezione. Per impostazione predefinita, la variabile `ErrorActionPreference` è impostata su Continua, a indicare che il processo continua a essere eseguito in caso di errore. Se la variabile di preferenza è impostata su Interrompi, il processo viene sospeso in caso di errore.  |
| Suspending |Si applica solo ai [runbook grafici e ai runbook del flusso di lavoro di PowerShell](automation-runbook-types.md). Il sistema sta provando a sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, viene completato prima di poter essere sospeso. |

## <a name="activity-logging"></a>Registrazione dell'attività

L'esecuzione dei runbook in Automazione di Azure scrive i dettagli in un log attività per l'account di Automazione. Per informazioni dettagliate sull'uso del log, vedere [Recuperare i dettagli dal log attività](manage-runbooks.md#retrieve-details-from-activity-log). 

## <a name="exceptions"></a>Eccezioni

Questa sezione descrive alcuni modi per gestire le eccezioni o i problemi intermittenti nei runbook. Un esempio è l'eccezione WebSocket. La corretta gestione delle eccezioni impedisce che gli errori di rete temporanei causino un errore del runbook. 

### <a name="erroractionpreference"></a>ErrorActionPreference

La variabile [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina il modo in cui PowerShell risponde a un errore non fatale. Gli errori fatali terminano sempre un processo e non sono interessati da `ErrorActionPreference`.

Quando il runbook usa `ErrorActionPreference`, un errore che normalmente non è fatale, ad esempio `PathNotFound` del cmdlet [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) impedisce il completamento del runbook. L'esempio seguente mostra l'uso di `ErrorActionPreference`. Il comando [Write-Output](/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) finale non viene mai eseguito, perché lo script si interrompe.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) viene usato negli script di PowerShell per gestire gli errori fatali. Lo script può usare questo meccanismo per intercettare eccezioni specifiche o eccezioni generali. L'istruzione `catch` deve essere usata per tenere traccia o tentare di gestire gli errori. L'esempio seguente tenta di scaricare un file che non esiste. Rileva l'eccezione `System.Net.WebException` e restituisce l'ultimo valore per un'altra eccezione.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) può essere usato per generare un errore fatale. Questo meccanismo può essere utile quando si definisce la logica personalizzata in un runbook. Se lo script soddisfa un criterio che dovrebbe arrestarlo, può usare l'istruzione `throw` per eseguire l'interruzione. L'esempio seguente usa questa istruzione per mostrare un parametro di funzione obbligatorio.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

Il runbook deve gestire gli errori. Automazione di Azure supporta due tipi di errori di PowerShell, fatali e non fatali. 

Quando si verificano, gli errori fatali interrompono l'esecuzione del runbook. Il runbook si interrompe e lo stato del processo è Non riuscito.

Gli errori non fatali consentono allo script di continuare anche dopo che questi si sono verificati. Un esempio di errore non fatale è quello che si verifica quando il runbook usa il cmdlet `Get-ChildItem` con un percorso che non esiste. PowerShell rileva che il percorso non esiste, genera un errore e passa alla cartella successiva. In questo caso, l'errore non imposta lo stato del processo del runbook su Non riuscito e il processo potrebbe anche essere completato. Per forzare l'arresto di un runbook in caso di errore non fatale, è possibile usare `ErrorAction Stop` nel cmdlet.

## <a name="calling-processes"></a>Processi di chiamata

I runbook eseguiti nelle sandbox di Azure non supportano i processi di chiamata, ad esempio file eseguibili (file con estensione **EXE**) o sottoprocessi. Il motivo è che una sandbox di Azure è un processo condiviso eseguito in un contenitore che potrebbe non essere in grado di accedere a tutte le API sottostanti. Per gli scenari che richiedono software di terze parti o chiamate ai sottoprocessi, è consigliabile eseguire un runbook in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Caratteristiche dell'applicazione e del dispositivo

I processi di runbook nelle sandbox di Azure non possono accedere alle caratteristiche del dispositivo o dell'applicazione. L'API più comunemente usata per eseguire query sulle metriche delle prestazioni in Windows è WMI e alcune delle metriche comuni sono l'utilizzo della memoria e della CPU. Tuttavia, non importa quale API viene usata, perché i processi in esecuzione nel cloud non possono accedere all'implementazione Microsoft di Web-Based Enterprise Management (WBEM). Questa piattaforma si basa su Common Information Model (CIM) e offre gli standard di settore per la definizione delle caratteristiche del dispositivo e dell'applicazione.

## <a name="webhooks"></a>Webhook

I servizi esterni, ad esempio Azure DevOps Services e GitHub, possono avviare un runbook in Automazione di Azure. Per eseguire questo tipo di avvio, il servizio usa un [webhook](automation-webhooks.md) tramite una sola richiesta HTTP. L'uso di un webhook consente l'avvio dei runbook senza l'implementazione di una funzionalità di Automazione di Azure completa. 

## <a name="shared-resources"></a><a name="fair-share"></a>Risorse condivise

Per condividere le risorse tra tutti runbook nel cloud, Azure usa un concetto denominato condivisione equa. Con una condivisione equa, Azure scarica o arresta temporaneamente i processi eseguiti per più di tre ore. I processi dei [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e dei [runbook di Python](automation-runbook-types.md#python-runbooks) vengono arrestati, non vengono riavviati e lo stato del processo risulta Arrestato.

Per le attività a esecuzione prolungata di Automazione di Azure è consigliabile usare un ruolo di lavoro ibrido per runbook. I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Anche se i ruoli di lavoro ibridi per runbook non sono soggetti al limite di 3 ore della condivisione equa, i runbook devono essere sviluppati per essere eseguiti nei ruoli di lavoro che supportano il riavvio causato da problemi imprevisti dell'infrastruttura locale.

Un'altra opzione consiste nell'ottimizzare il runbook usando un runbook figlio. Ad esempio, il runbook potrebbe eseguire il ciclo della stessa funzione su diverse risorse, ad esempio, con un'operazione di database su diversi database. È possibile spostare questa funzione in un [runbook figlio](automation-child-runbooks.md) e fare in modo che il runbook lo chiami usando [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). I runbook figlio vengono eseguiti in parallelo in processi separati.

L'uso di runbook figlio riduce la quantità totale di tempo per il completamento del runbook padre. Il runbook può usare il cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) per verificare lo stato del processo di un runbook figlio se dispone di altre operazioni dopo il completamento del runbook figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare un runbook PowerShell, vedere [Esercitazione: Creare un runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Per usare i runbook, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* Per informazioni dettagliate su PowerShell, vedere [Documentazione di PowerShell](/powershell/scripting/overview).
* * Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
