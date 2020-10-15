---
title: Risolvere i problemi di distribuzione relativi alla funzionalità Automazione di Azure
description: Questo articolo spiega come risolvere i problemi che si verificano durante la distribuzione della funzionalità Automazione di Azure.
services: automation
ms.date: 06/30/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: a6b8384193e821e6c41a0d4d979cda51f6c65b3a
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070487"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Risolvere i problemi relativi alla distribuzione di funzionalità

Quando si distribuisce la funzionalità Rilevamento modifiche e inventario o Gestione aggiornamenti di Automazione di Azure nelle macchine virtuali, potrebbero venire visualizzati dei messaggi di errore. Questo articolo descrive gli errori che potrebbero verificarsi e fornisce indicazioni per la loro risoluzione.

## <a name="known-issues"></a>Problemi noti

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: la ridenominazione di un nodo registrato richiede l'annullamento o la ripetizione della registrazione

#### <a name="issue"></a>Problema

Dopo la registrazione di un nodo in Automazione di Azure, il nome del computer del sistema operativo viene modificato. Tuttavia, i report del nodo continuano ad apparire con il nome originale.

#### <a name="cause"></a>Causa

La ridenominazione dei nodi registrati non determina l'aggiornamento del nome del nodo in Automazione di Azure.

#### <a name="resolution"></a>Risoluzione

Annullare la registrazione del nodo dalla configurazione dello stato di Automazione di Azure e quindi registrarlo di nuovo. I report pubblicati nel servizio prima di questa operazione non saranno più disponibili.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Scenario: la ripetizione della firma dei certificati tramite il proxy HTTPS non è supportata

#### <a name="issue"></a>Problema

Quando ci si connette tramite un proxy che interrompe il traffico HTTPS e quindi esegue di nuovo la crittografia del traffico utilizzando un nuovo certificato, il servizio non consente la connessione.

#### <a name="cause"></a>Causa

Automazione di Azure non supporta la ripetizione della firma dei certificati usati per crittografare il traffico.

#### <a name="resolution"></a>Risoluzione

Attualmente non è disponibile alcuna soluzione alternativa per questo problema.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: la distribuzione della funzionalità ha esito negativo e viene visualizzato il messaggio "Non è possibile abilitare la soluzione"

#### <a name="issue"></a>Problema

Quando si tenta di abilitare una funzionalità in una macchina virtuale, viene visualizzato uno dei messaggi seguenti:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Questo errore è causato da autorizzazioni non corrette o mancanti nella macchina virtuale o nell'area di lavoro oppure per l'utente.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di disporre delle [autorizzazioni per la distribuzione delle funzionalità](../automation-role-based-access-control.md#feature-setup-permissions) appropriate, quindi riprovare a distribuire la funzionalità. Se viene visualizzato il messaggio di errore `The solution cannot be enabled on this VM because the permission to read the workspace is missing` , vedere le seguenti [informazioni sulla risoluzione dei problemi](update-management.md#failed-to-enable-error).

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: la distribuzione della funzionalità non riesce e viene visualizzato il messaggio "Non è stato possibile configurare l'account di Automazione per la registrazione diagnostica"

#### <a name="issue"></a>Problema

Quando si tenta di abilitare una funzionalità in una macchina virtuale, viene visualizzato il messaggio seguente:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Questo errore può essere causato da un piano tariffario che non corrisponde al modello di fatturazione della sottoscrizione. Per altre informazioni, vedere [Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure](../../azure-monitor/platform/usage-estimated-costs.md).

#### <a name="resolution"></a>Risoluzione

Creare manualmente l'area di lavoro Log Analytics e ripetere il processo di distribuzione della funzionalità per selezionare l'area di lavoro creata.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Questo codice di errore indica che il formato della query sul gruppo di computer oggetto della ricerca salvata usata per individuare la funzionalità non è corretto. 

#### <a name="cause"></a>Causa

È possibile che la query sia stata modificata dall'utente o dal sistema.

#### <a name="resolution"></a>Risoluzione

È possibile eliminare la query per la funzionalità, quindi abilitare di nuovo la funzionalità per ricreare la query. La query è disponibile nell'area di lavoro **Ricerche salvate**. Il nome della query è **MicrosoftDefaultComputerGroup** e la categoria della query corrisponde al nome associato alla funzionalità. Se sono abilitate più funzionalità, la query **MicrosoftDefaultComputerGroup** viene mostrata più volte nell'area **Ricerche salvate**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problema

Questo codice di errore indica che la distribuzione non è riuscita a causa della violazione di uno o più criteri.

#### <a name="cause"></a>Causa 

Alcuni criteri impedisco il completamento dell'operazione.

#### <a name="resolution"></a>Risoluzione

Per poter distribuire la funzionalità, provare a modificare i criteri indicati. È possibile definire vari tipi di criteri diversi, pertanto le modifiche necessarie dipendono dal tipo di criterio violato. Ad esempio, se un criterio viene definito in un gruppo di risorse che nega l'autorizzazione per la modifica dei contenuti di alcune risorse incluse, è possibile scegliere una di queste soluzioni:

* Rimuovere completamente il criterio.
* Provare ad abilitare la funzionalità per un gruppo di risorse diverso.
* Ridefinire la destinazione dei criteri per una risorsa specifica, ad esempio un account di Automazione.
* Modificare il set di risorse per il quale il criterio specifico è configurato per il rifiuto dell'autorizzazione.

Controllare le notifiche nell'angolo superiore destro del portale di Azure o passare al gruppo di risorse che contiene l'account di Automazione e selezionare **Distribuzioni** in **Impostazioni** per visualizzare la distribuzione non riuscita. Per altre informazioni su Criteri di Azure, vedere [Panoramica dei criteri di Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: errori durante il tentativo di scollegare un'area di lavoro

#### <a name="issue"></a>Problema

Quando si tenta di scollegare un'area di lavoro, viene visualizzato il messaggio di errore seguente:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando sono presenti funzionalità ancora attive nell'area di lavoro di Log Analytics che dipendono dall'account di Automazione e dall'area di lavoro Log Analytics da collegare.

### <a name="resolution"></a>Risoluzione

Se si stanno usando le risorse per le funzionalità indicate di seguito, rimuoverle dall'area di lavoro:

* Gestione degli aggiornamenti
* Rilevamento modifiche e inventario
* Avviare/arrestare VM durante gli orari di minore attività

Dopo aver rimosso le risorse della funzionalità, è possibile scollegare l'area di lavoro. È importante eseguire la pulizia di tutti gli artefatti esistenti per queste funzionalità nell'area di lavoro e nell'account di Automazione:

* Per Gestione aggiornamenti, rimuovere le **distribuzioni di aggiornamento (pianificazioni)** dall'account di Automazione.
* Per Avvio/Arresto di macchine virtuali durante gli orari di minore attività, rimuovere i blocchi per i componenti delle funzionalità nell'account di Automazione in **Impostazioni** > **Blocchi**. Per ulteriori informazioni, vedere [Rimuovere la funzionalità](../automation-solution-vm-management.md#remove-the-feature).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Errori dell'estensione Log Analytics per Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

L'installazione dell'estensione dell'agente Log Analytics per Windows può avere esito negativo per diversi motivi. Nella sezione seguente vengono descritti i problemi di distribuzione della funzionalità che possono causare errori durante la distribuzione dell'estensione dell'agente Log Analytics per Windows.

>[!NOTE]
>Agente di Log Analytics per Windows è il nome attualmente in uso in Automazione di Azure per l'agente Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: È stata generata un'eccezione durante una richiesta WebClient

L'estensione Log Analytics per Windows nella macchina virtuale non può comunicare con le risorse esterne e la distribuzione non riesce.

#### <a name="issue"></a>Problema

Di seguito sono riportati esempi di messaggi di errore restituiti:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Alcune cause possibili di questo errore sono:

* Un proxy configurato nella macchina virtuale consente solo porte specifiche.
* Un'impostazione del firewall ha bloccato l'accesso alle porte e agli indirizzi necessari.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di avere le porte e gli indirizzi aperti per la comunicazione. Per un elenco di porte e indirizzi, vedere la [Pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: l'installazione non è riuscita a causa di problemi temporanei dell'ambiente

Durante la distribuzione, l'installazione dell'estensione dell'agente Log Analytics per Windows non è riuscita a causa di un'altra installazione o di un'azione che l'hanno bloccata.

#### <a name="issue"></a>Problema

Di seguito sono riportati alcuni esempi di messaggi di errore che è potrebbero venire visualizzati:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Causa

Alcune cause possibili di questo errore sono:

* È in corso un'altra installazione.
* È stato attivato il riavvio del sistema durante la distribuzione dei modelli.

#### <a name="resolution"></a>Risoluzione

Questo errore è di natura temporanea. Ritentare la distribuzione per installare l'estensione.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: Timeout dell'installazione

L'installazione dell'estensione Log Analytics per Windows non è stata completata a causa di un timeout.

#### <a name="issue"></a>Problema

Di seguito è riportato un esempio di un messaggio di errore che potrebbe venire visualizzato:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Questo tipo di errore si verifica perché la macchina virtuale è sottoposta a un carico eccessivo durante l'installazione.

### <a name="resolution"></a>Risoluzione

Provare a installare l'estensione dell'agente Log Analytics per Windows quando la macchina virtuale è sottoposta a un carico di lavoro inferiore.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure consente di entrare in contatto con la community di Azure e quindi di ottenere risposte, assistenza e consulenza.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.