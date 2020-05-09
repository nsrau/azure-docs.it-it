---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 1933688459cd02ee4da448d2e83b0a7a92a1d2c8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994749"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

Automazione dei processi in automazione di Azure consente di creare e gestire PowerShell, il flusso di lavoro PowerShell e manuali operativi grafici. Per informazioni dettagliate, vedere [manuali operativi di automazione di Azure](automation-runbook-types.md). 

L'automazione esegue il manuali operativi in base alla logica definita al suo interno. Se un Runbook viene interrotto, viene riavviato all'inizio. Questo comportamento richiede la scrittura di manuali operativi che supportano il riavvio se si verificano problemi temporanei.

L'avvio di un Runbook in automazione di Azure crea un processo, ovvero una singola istanza di esecuzione di Runbook. Ogni processo accede alle risorse di Azure effettuando una connessione alla sottoscrizione di Azure. Il processo può accedere solo alle risorse nel Data Center se tali risorse sono accessibili dal cloud pubblico.

Automazione di Azure assegna a un thread di lavoro l'esecuzione di ogni processo durante l'esecuzione del Runbook. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non è possibile controllare quali servizi del ruolo di lavoro richiedono.

Quando si visualizza l'elenco di manuali operativi nella portale di Azure, viene visualizzato lo stato di ogni processo avviato per ogni Runbook. Automazione di Azure archivia i log dei processi per un massimo di 30 giorni.

Il diagramma seguente mostra il ciclo di vita di un processo Runbook per [PowerShell manuali operativi](automation-runbook-types.md#powershell-runbooks), [manuali operativi del flusso](automation-runbook-types.md#powershell-workflow-runbooks)di lavoro PowerShell e [manuali operativi grafici](automation-runbook-types.md#graphical-runbooks).

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="runbook-execution-environment"></a>Ambiente di esecuzione Runbook

Manuali operativi in automazione di Azure può essere eseguito in una sandbox di Azure o in un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md). 

Quando manuali operativi sono progettati per l'autenticazione e l'esecuzione di risorse in Azure, vengono eseguiti in una sandbox di Azure, un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni di risorse della sandbox. L'ambiente sandbox di Azure non supporta operazioni interattive. Impedisce l'accesso a tutti i server COM out-of-process. Richiede anche l'uso di file MOF locali per manuali operativi che effettuano chiamate Win32.

È anche possibile usare un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md) per eseguire manuali operativi direttamente sul computer che ospita il ruolo e sulle risorse locali nell'ambiente. Automazione di Azure archivia e gestisce manuali operativi e li recapita a uno o più computer assegnati.

>[!NOTE]
>Per l'esecuzione in un ruolo di lavoro ibrido per Runbook Linux, gli script devono essere firmati e il ruolo di lavoro configurato di conseguenza. In alternativa, la [convalida della firma deve essere](automation-linux-hrw-install.md#turn-off-signature-validation)disattivata. 

Nella tabella seguente sono elencate alcune attività di esecuzione di runbook con l'ambiente di esecuzione consigliato elencato per ciascuna di esse.

|Attività|Recommendation|Note|
|---|---|---|
|Integrazione con le risorse di Azure|Sandbox di Azure|Ospitata in Azure, l'autenticazione è più semplice. Se si usa un ruolo di lavoro ibrido per Runbook in una macchina virtuale di Azure, è possibile [usare l'autenticazione runbook con le identità gestite](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Ottenere prestazioni ottimali per la gestione delle risorse di Azure|Sandbox di Azure|Lo script viene eseguito nello stesso ambiente, con minore latenza.|
|Ridurre al minimo i costi operativi|Sandbox di Azure|Non è previsto alcun overhead di calcolo e non è necessaria alcuna macchina virtuale.|
|Esegui script con esecuzione prolungata|Ruolo di lavoro ibrido per runbook|Le sandbox di Azure hanno [limiti di risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagire con i servizi locali|Ruolo di lavoro ibrido per runbook|Accedere direttamente al computer host o alle risorse in altri ambienti cloud o nell'ambiente locale. |
|Richiedi software e file eseguibili di terze parti|Ruolo di lavoro ibrido per runbook|È possibile gestire il sistema operativo e installare il software.|
|Monitorare un file o una cartella con un runbook|Ruolo di lavoro ibrido per runbook|Usare un' [attività Watcher](automation-watchers-tutorial.md) in un ruolo di lavoro ibrido per Runbook.|
|Eseguire uno script con utilizzo intensivo delle risorse|Ruolo di lavoro ibrido per runbook| Le sandbox di Azure hanno [limiti di risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Usare moduli con requisiti specifici| Ruolo di lavoro ibrido per runbook|Ad esempio:</br> WinSCP - dipendenza da winscp.exe </br> Amministrazione di IIS: dipendenza dall'abilitazione o gestione di IIS|
|Installare un modulo con un programma di installazione|Ruolo di lavoro ibrido per runbook|I moduli per sandbox devono supportare la copia.|
|Usare manuali operativi o moduli che richiedono la versione di .NET Framework diversa da 4.7.2|Ruolo di lavoro ibrido per runbook|Azure sandbox supporta .NET Framework 4.7.2 e l'aggiornamento a una versione diversa non è supportato.|
|Eseguire script che richiedono l'elevazione dei privilegi|Ruolo di lavoro ibrido per runbook|Le sandbox non consentono l'elevazione dei privilegi. Con un ruolo di lavoro ibrido per Runbook è possibile disattivare il controllo dell'account utente e usare [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) quando si esegue il comando che richiede l'elevazione dei privilegi.|
|Eseguire script che richiedono l'accesso a Strumentazione gestione Windows (WMI)|Ruolo di lavoro ibrido per runbook|I processi in esecuzione in sandbox nel cloud non possono accedere al provider WMI. |

## <a name="resources"></a>Risorse

Il manuali operativi deve includere la logica per gestire [le risorse](https://docs.microsoft.com/rest/api/resources/resources), ad esempio le macchine virtuali, la rete e le risorse sulla rete. Le risorse sono associate a una sottoscrizione di Azure e manuali operativi richiedono credenziali appropriate per accedere a qualsiasi risorsa. Per un esempio di gestione delle risorse in un Runbook, vedere [gestire le risorse](manage-runbooks.md#handle-resources). 

## <a name="security"></a>Sicurezza

Automazione di Azure usa il [Centro sicurezza di Azure (ASC)](../security-center/security-center-intro.md) per garantire la sicurezza delle risorse e rilevare compromessi nei sistemi Linux. La sicurezza viene garantita tra i carichi di lavoro, indipendentemente dal fatto che le risorse siano in Azure o meno. Vedere [Introduzione all'autenticazione in automazione di Azure](automation-security-overview.md).

ASC pone vincoli sugli utenti che possono eseguire qualsiasi script, firmato o senza segno, in una macchina virtuale. Se si è un utente con accesso alla radice di una macchina virtuale, è necessario configurarlo in modo esplicito con una firma digitale o disattivarlo. In caso contrario, è possibile eseguire uno script solo per applicare gli aggiornamenti del sistema operativo dopo la creazione di un account di automazione e l'abilitazione della funzionalità appropriata.

## <a name="subscriptions"></a>Sottoscrizioni

Una [sottoscrizione](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) di Azure è un contratto con Microsoft per l'uso di uno o più servizi basati su cloud, per i quali viene addebitato il costo. Per automazione di Azure, ogni sottoscrizione è collegata a un account di automazione di Azure ed è possibile [creare più sottoscrizioni](manage-runbooks.md#work-with-multiple-subscriptions) nell'account.

## <a name="credentials"></a>Credenziali

Un Runbook richiede le [credenziali](shared-resources/credentials.md) appropriate per accedere a qualsiasi risorsa, sia per i sistemi di Azure che per quelli di terze parti. Queste credenziali vengono archiviate in automazione di Azure, Key Vault e così via.  

## <a name="azure-monitor"></a>Monitoraggio di Azure

Automazione di Azure usa il monitoraggio di [Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per monitorare le operazioni del computer. Per le operazioni è necessario disporre di un'area di lavoro Log Analytics e di [log Analytics agenti](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

### <a name="log-analytics-agent-for-windows"></a>Agente di Log Analytics per Windows

L' [agente di log Analytics per Windows](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows) funziona con monitoraggio di Azure per gestire le macchine virtuali Windows e i computer fisici. I computer possono essere in esecuzione in Azure o in un ambiente non Azure, ad esempio un Data Center locale. È necessario configurare l'agente per la segnalazione a una o più aree di lavoro Log Analytics. 

>[!NOTE]
>L'agente di Log Analytics per Windows era noto in precedenza come Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agente di Log Analytics per Linux

L' [agente di log Analytics per Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux) funziona in modo simile all'agente per Windows, ma connette i computer Linux a monitoraggio di Azure. L'agente viene installato con un account utente **nxautomation** che consente l'esecuzione di comandi che richiedono autorizzazioni radice, ad esempio, in un ruolo di lavoro ibrido per Runbook. L'account **nxautomation** è un account di sistema che non richiede una password. 

L'account **nxautomation** con le autorizzazioni sudo corrispondenti deve essere presente durante [l'installazione di un ruolo di lavoro ibrido per Runbook di Linux](automation-linux-hrw-install.md). Se si tenta di installare il ruolo di lavoro e l'account non è presente o non dispone delle autorizzazioni appropriate, l'installazione non riesce.

I log disponibili per l'agente Log Analytics e l'account **nxautomation** sono i seguenti:

* /var/opt/Microsoft/omsagent/log/omsagent.log-log agente Log Analytics 
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log-log del ruolo di lavoro di automazione

>[!NOTE]
>L'utente **nxautomation** caricato come parte di gestione aggiornamenti esegue solo manuali operativi con segno.

## <a name="runbook-permissions"></a>Autorizzazioni per i runbook

Un Runbook richiede le autorizzazioni per l'autenticazione in Azure tramite le credenziali. Vedere [gestire gli account RunAs di automazione di Azure](manage-runas-account.md). 

## <a name="modules"></a>Moduli

Automazione di Azure supporta una serie di moduli predefiniti, inclusi alcuni moduli AzureRM (AzureRM. Automation) e un modulo che contiene diversi cmdlet interni. Sono inoltre supportati moduli installabili, tra cui AZ Modules (AZ. Automation), attualmente in uso in preferenza ai moduli AzureRM. Per informazioni dettagliate sui moduli disponibili per le configurazioni manuali operativi e DSC, vedere gestire i [moduli in automazione di Azure](shared-resources/modules.md).

## <a name="certificates"></a>Certificati

Automazione di Azure usa i [certificati](shared-resources/certificates.md) per l'autenticazione in Azure o li aggiunge a risorse di Azure o di terze parti. I certificati vengono archiviati in modo sicuro per l'accesso da parte di manuali operativi e delle configurazioni DSC. 

Il manuali operativi può usare certificati autofirmati, che non sono firmati da un'autorità di certificazione (CA). Vedere [creare un nuovo certificato](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Processi

Automazione di Azure supporta un ambiente per l'esecuzione di processi dallo stesso account di automazione. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox. 

I processi in esecuzione nello stesso processo sandbox possono interessare l'uno con l'altro. Un esempio è l'esecuzione del cmdlet [Disconnect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) . L'esecuzione di questo cmdlet disconnette tutti i processi del Runbook nel processo sandbox condiviso. Per un esempio di utilizzo di questo scenario, vedere [Impedisci processi simultanei](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>I processi di PowerShell avviati da un Runbook eseguito in una sandbox di Azure potrebbero non essere eseguiti nella [modalità del linguaggio PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes)completo. 

### <a name="job-statuses"></a>Stati dei processi

Nella tabella seguente vengono descritti gli stati possibili per un processo. È possibile visualizzare un riepilogo dello stato per tutti i processi di Runbook o esaminare i dettagli di uno specifico processo del Runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con i log di monitoraggio di Azure, vedere [trasmettere lo stato del processo e i flussi del processo dall'automazione ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md). Vedere anche [ottenere gli Stati dei processi](manage-runbooks.md#obtain-job-statuses) per un esempio di utilizzo degli Stati in un Runbook.

| Stato | Descrizione |
|:--- |:--- |
| Completi |Il processo è stato completato. |
| Operazione non riuscita |Impossibile compilare un Runbook grafico o un flusso di lavoro PowerShell. Non è stato possibile avviare un Runbook di PowerShell o si è verificata un'eccezione nel processo. Vedere [tipi di Runbook di automazione di Azure](automation-runbook-types.md).|
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Queued |Il processo è in attesa che le risorse di un ruolo di lavoro di automazione diventino disponibili per poter essere avviate. |
| Resuming |Il sistema sta riprendendo il processo dopo che è stato sospeso. |
| In esecuzione |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di condivisione equa . Riprenderà a breve dall'ultimo checkpoint. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Si applica solo a [manuali operativi grafico e del flusso di lavoro PowerShell](automation-runbook-types.md) . Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Se un Runbook non ha un checkpoint, inizia dall'inizio. Se ha un checkpoint, può essere nuovamente avviato e riprendere dall'ultimo checkpoint. Il sistema sospende il Runbook solo quando si verifica un'eccezione. Per impostazione predefinita, `ErrorActionPreference` la variabile è impostata su continue (continua), a indicare che il processo viene mantenuto in un errore. Se la variabile di preferenza è impostata su Stop, il processo viene sospeso in caso di errore.  |
| Suspending |Si applica solo a [manuali operativi grafico e del flusso di lavoro PowerShell](automation-runbook-types.md) . Il sistema sta provando a sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, viene completato prima di poter essere sospeso. |

## <a name="activity-logging"></a>Registrazione delle attività

L'esecuzione di manuali operativi in automazione di Azure scrive i dettagli in un log attività per l'account di automazione. Per informazioni dettagliate sull'uso del log, vedere [recuperare i dettagli dal log attività](manage-runbooks.md#retrieve-details-from-activity-log). 

## <a name="exceptions"></a>Eccezioni

Questa sezione descrive alcuni modi per gestire le eccezioni o i problemi intermittenti nei manuali operativi. Un esempio è un'eccezione WebSocket. La corretta gestione delle eccezioni impedisce che gli errori di rete temporanei causino un errore di manuali operativi. 

### <a name="erroractionpreference"></a>ErrorActionPreference

La variabile [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina il modo in cui PowerShell risponde a un errore non fatale. Gli errori di terminazione terminano sempre e non `ErrorActionPreference`sono interessati da.

Quando Runbook USA `ErrorActionPreference`, un errore normalmente non fatale, ad esempio `PathNotFound` dal cmdlet [Get-ChildItem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) , impedisce il completamento di Runbook. L'esempio seguente illustra l'uso di `ErrorActionPreference`. Il comando [Write-Output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) finale non viene mai eseguito, perché lo script viene arrestato.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try catch finally

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) viene usato negli script di PowerShell per gestire gli errori di terminazione. Lo script può utilizzare questo meccanismo per intercettare eccezioni specifiche o eccezioni generali. L' `catch` istruzione deve essere utilizzata per rilevare o tentare di gestire gli errori. Nell'esempio seguente viene tentato di scaricare un file che non esiste. Viene intercettata l' `System.Net.WebException` eccezione e viene restituito l'ultimo valore per qualsiasi altra eccezione.

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) può essere usato per generare un errore di terminazione. Questo meccanismo può essere utile quando si definisce la logica personalizzata in un Runbook. Se lo script soddisfa un criterio che dovrebbe arrestarlo, può utilizzare l' `throw` istruzione per arrestare. Nell'esempio seguente viene utilizzata l'istruzione per visualizzare un parametro di funzione obbligatorio.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

Il manuali operativi deve gestire gli errori. Automazione di Azure supporta due tipi di errori di PowerShell, che terminano e non terminano. 

Gli errori di chiusura interrompono l'esecuzione del runbook quando si verificano. Il Runbook si interrompe con lo stato del processo non riuscito.

Gli errori non fatali consentono a uno script di continuare anche dopo essersi verificati. Un esempio di errore non fatale è quello che si verifica quando un Runbook utilizza il `Get-ChildItem` cmdlet con un percorso che non esiste. PowerShell rileva che il percorso non esiste, genera un errore e passa alla cartella successiva. In questo caso, l'errore non imposta lo stato del processo del Runbook su Failed e il processo potrebbe essere anche completato. Per forzare l'arresto di un runbook in caso di errore non fatale, è possibile usare `ErrorAction Stop` nel cmdlet.

## <a name="calling-processes"></a>Chiamata di processi

Manuali operativi eseguiti in sandbox di Azure non supportano processi di chiamata, ad esempio file eseguibili (file con**estensione exe** ) o sottoprocessi. Il motivo è che Azure sandbox è un processo condiviso eseguito in un contenitore che potrebbe non essere in grado di accedere a tutte le API sottostanti. Per gli scenari che richiedono software di terze parti o chiamate a sottoprocessi, è consigliabile eseguire un Runbook in un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Caratteristiche del dispositivo e dell'applicazione

I processi Runbook in sandbox di Azure non possono accedere alle caratteristiche del dispositivo o dell'applicazione. L'API più comune utilizzata per eseguire query sulle metriche delle prestazioni in Windows è WMI e alcune delle metriche comuni sono l'utilizzo della memoria e della CPU. Tuttavia, non importa quale API viene usata, perché i processi in esecuzione nel cloud non possono accedere all'implementazione Microsoft di Web-Based Enterprise Management (WBEM). Questa piattaforma è basata sulla Common Information Model (CIM), che fornisce gli standard di settore per la definizione delle caratteristiche del dispositivo e dell'applicazione.

## <a name="webhooks"></a>Webhook

I servizi esterni, ad esempio Azure DevOps Services e GitHub, possono avviare un Runbook in automazione di Azure. Per eseguire questo tipo di avvio, il servizio usa un [webhook](automation-webhooks.md) tramite una singola richiesta HTTP. L'uso di un webhook consente di avviare manuali operativi senza implementare una soluzione completa di automazione di Azure. 

## <a name="shared-resources"></a><a name="fair-share"></a>Risorse condivise

Per condividere le risorse tra tutti manuali operativi nel cloud, Azure usa un concetto denominato condivisione equa. Con una condivisione equa, Azure Scarica o arresta temporaneamente qualsiasi processo eseguito per più di tre ore. I processi per [manuali operativi di PowerShell](automation-runbook-types.md#powershell-runbooks) e [manuali operativi Python](automation-runbook-types.md#python-runbooks) vengono arrestati e non riavviati e lo stato del processo viene arrestato.

Per le attività di automazione di Azure con esecuzione prolungata, è consigliabile usare un ruolo di lavoro ibrido per Runbook. I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Sebbene i ruoli di lavoro ibridi per Runbook non siano limitati dal limite di tre ore, è necessario sviluppare manuali operativi per l'esecuzione nei ruoli di lavoro che supportano i riavvii da problemi di infrastruttura locale imprevisti.

Un'altra opzione consiste nell'ottimizzare un Runbook usando manuali operativi figlio. Ad esempio, il Runbook potrebbe eseguire il ciclo della stessa funzione su diverse risorse, ad esempio, con un'operazione di database su diversi database. È possibile spostare questa funzione in un [Runbook figlio](automation-child-runbooks.md) e fare in modo che Runbook la chiami usando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). I manuali operativi figlio vengono eseguiti in parallelo in processi distinti.

L'uso di manuali operativi figlio riduce la quantità totale di tempo necessario per il completamento del Runbook padre. Il Runbook può usare il cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) per controllare lo stato del processo per un Runbook figlio se dispone ancora di più operazioni dopo il completamento del figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare un Runbook, vedere [gestire manuali operativi in automazione di Azure](manage-runbooks.md).
* Per altre informazioni su PowerShell, incluse le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
