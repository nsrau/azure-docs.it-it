---
title: Risoluzione dei problemi relativi all'onboarding delle soluzioni di gestione di automazione di Azure
description: Informazioni su come risolvere gli errori di onboarding della soluzione di automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 371094ecba5168fd32a7af9fb81a71eb722efc91
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836530"
---
# <a name="troubleshoot-solution-onboarding"></a>Risolvere i problemi di caricamento della soluzione

È possibile che vengano visualizzati messaggi di errore quando si esegue il caricamento della soluzione Gestione aggiornamenti di automazione di Azure o la soluzione Rilevamento modifiche e di inventario. Questo articolo descrive i vari errori che possono verificarsi e come risolverli.

## <a name="known-issues"></a>Problemi noti

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: per rinominare un nodo registrato è necessario annullare la registrazione o la registrazione

#### <a name="issue"></a>Problema

Un nodo viene registrato in automazione di Azure e quindi il nome del computer del sistema operativo viene modificato. I report del nodo continuano a essere visualizzati con il nome originale.

#### <a name="cause"></a>Causa

La ridenominazione dei nodi registrati non comporta l'aggiornamento del nome del nodo in automazione di Azure.

#### <a name="resolution"></a>Soluzione

Annullare la registrazione del nodo dalla configurazione dello stato di automazione di Azure e quindi registrarlo di nuovo. I report pubblicati nel servizio prima del momento non saranno più disponibili.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Scenario: la nuova firma dei certificati tramite il proxy HTTPS non è supportata

#### <a name="issue"></a>Problema

Quando ci si connette tramite una soluzione proxy che termina il traffico HTTPS e quindi si crittografa di nuovo il traffico utilizzando un nuovo certificato, il servizio non consente la connessione.

#### <a name="cause"></a>Causa

Automazione di Azure non supporta la ripetizione della firma dei certificati usati per crittografare il traffico.

#### <a name="resolution"></a>Soluzione

Attualmente non è disponibile alcuna soluzione per questo problema.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-onboarding-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: l'onboarding ha esito negativo con il messaggio "Impossibile abilitare la soluzione"

#### <a name="issue"></a>Problema

Quando si tenta di caricare una macchina virtuale in una soluzione, viene visualizzato uno dei messaggi seguenti:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Questo errore è causato da autorizzazioni errate o mancanti nella macchina virtuale o nell'area di lavoro o per l'utente.

#### <a name="resolution"></a>Soluzione

Assicurarsi [di disporre delle autorizzazioni corrette necessarie per](../automation-role-based-access-control.md#onboarding-permissions)caricare i computer, quindi provare di nuovo a eseguire l'onboarding della soluzione. Se viene visualizzato il messaggio `The solution cannot be enabled on this VM because the permission to read the workspace is missing`di errore, assicurarsi di disporre dell' `Microsoft.OperationalInsights/workspaces/read` autorizzazione per essere in grado di verificare se la macchina virtuale è stata caricata in un'area di lavoro.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: l'onboarding ha esito negativo con il messaggio "Impossibile configurare l'account di automazione per la registrazione diagnostica"

#### <a name="issue"></a>Problema

Quando si tenta di caricare una macchina virtuale in una soluzione, viene visualizzato il messaggio seguente:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Questo errore può essere causato se il piano tariffario non corrisponde al modello di fatturazione della sottoscrizione. Per altre informazioni, vedere [monitoraggio dell'utilizzo e dei costi stimati in monitoraggio di Azure](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Soluzione

Creare manualmente l'area di lavoro Log Analytics e ripetere il processo di onboarding per selezionare l'area di lavoro creata.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Questo codice di errore indica che la query del gruppo di computer di ricerca salvata utilizzata come destinazione della soluzione non è formattata correttamente. 

#### <a name="cause"></a>Causa

È possibile che la query sia stata modificata o che sia stata modificata dal sistema.

#### <a name="resolution"></a>Soluzione

È possibile eliminare la query per la soluzione e quindi eseguire di nuovo l'onboarding della soluzione, che consente di ricreare la query. È possibile trovare la query nell'area di lavoro in **ricerche salvate**. Il nome della query è **MicrosoftDefaultComputerGroup**e la categoria della query è il nome della soluzione associata. Se sono abilitate più soluzioni, la query **MicrosoftDefaultComputerGroup** viene visualizzata più volte in **ricerche salvate**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problema

Questo codice di errore indica che la distribuzione non è riuscita a causa di una violazione di uno o più criteri.

#### <a name="cause"></a>Causa 

Un criterio impedisce il completamento dell'operazione.

#### <a name="resolution"></a>Soluzione

Per distribuire correttamente la soluzione, è necessario valutare la possibilità di modificare i criteri indicati. Poiché esistono molti tipi diversi di criteri che è possibile definire, le modifiche richieste dipendono dal criterio violato. Ad esempio, se un criterio viene definito in un gruppo di risorse che nega l'autorizzazione per modificare il contenuto di alcune risorse contenute, è possibile scegliere una di queste correzioni:

* Rimuovere completamente il criterio.
* Provare a caricare la soluzione in un gruppo di risorse diverso.
* Ridestinare i criteri a una risorsa specifica, ad esempio un account di automazione.
* Rivedere il set di risorse che il criterio è configurato per negare.

Controllare le notifiche nell'angolo superiore destro della portale di Azure o andare al gruppo di risorse contenente l'account di automazione e selezionare **distribuzioni** in **Impostazioni** per visualizzare la distribuzione non riuscita. Per altre informazioni sui criteri di Azure, vedere [Panoramica di criteri di Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: errori durante il tentativo di scollegare un'area di lavoro

#### <a name="issue"></a>Problema

Quando si tenta di scollegare un'area di lavoro, viene visualizzato il messaggio di errore seguente:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando le soluzioni sono ancora attive nell'area di lavoro di Log Analytics che dipendono dall'account di automazione e dall'area di lavoro Log Analytics da collegare.

### <a name="resolution"></a>Soluzione

Se vengono usate, rimuovere le soluzioni seguenti dall'area di lavoro:

* Gestione degli aggiornamenti
* Rilevamento modifiche e inventario
* Avviare/arrestare VM durante gli orari di minore attività

Dopo aver rimosso le soluzioni, è possibile scollegare l'area di lavoro. È importante pulire tutti gli artefatti esistenti da queste soluzioni dall'area di lavoro e dall'account di automazione:

* Per Gestione aggiornamenti, rimuovere le **distribuzioni di aggiornamenti (pianificazioni)** dall'account di automazione.
* Per avvio/arresto di macchine virtuali durante gli orari di minore attività, rimuovere tutti i blocchi sui componenti della soluzione nell'account di automazione in **Impostazioni** > **blocchi**. Per ulteriori informazioni, vedere [la pagina relativa alla rimozione della soluzione avvio/arresto di macchine virtuali durante gli orari di minore attività](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics per gli errori di estensione Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Un'installazione dell'agente Log Analytics per l'estensione Windows può avere esito negativo per diversi motivi. Nella sezione seguente vengono descritti i problemi di onboarding che possono causare errori durante la distribuzione dell'estensione Log Analytics Agent per Windows.

>[!NOTE]
>Log Analytics Agent per Windows è il nome usato attualmente in automazione di Azure per il Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: è stata generata un'eccezione durante una richiesta WebClient

L'estensione Log Analytics per Windows nella macchina virtuale non è in grado di comunicare con le risorse esterne e la distribuzione ha esito negativo.

#### <a name="issue"></a>Problema

Di seguito sono riportati esempi di messaggi di errore restituiti:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Causa

Alcune possibili cause di questo errore sono:

* Un proxy configurato nella VM consente solo porte specifiche.
* Un'impostazione del firewall ha bloccato l'accesso alle porte e agli indirizzi necessari.

#### <a name="resolution"></a>Soluzione

Assicurarsi di avere le porte e gli indirizzi aperti per la comunicazione. Per un elenco di porte e indirizzi, vedere [pianificazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: installazione non riuscita a causa di problemi di ambiente temporanei

L'installazione dell'estensione Log Analytics per Windows non è riuscita durante la distribuzione a causa di un'altra installazione o azione che blocca l'installazione.

#### <a name="issue"></a>Problema

Di seguito sono riportati alcuni esempi di messaggi di errore che potrebbero essere restituiti:

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

Alcune possibili cause di questo errore sono:

* È in corso un'altra installazione.
* Il sistema viene attivato per il riavvio durante la distribuzione del modello.

#### <a name="resolution"></a>Soluzione

Questo errore è di natura temporanea. Ritentare la distribuzione per installare l'estensione.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: timeout dell'installazione

L'installazione dell'estensione Log Analytics per Windows non è stata completata a causa di un timeout.

#### <a name="issue"></a>Problema

Di seguito è riportato un esempio di un messaggio di errore che potrebbe essere restituito:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Questo tipo di errore si verifica perché la macchina virtuale è sottoposta a un carico eccessivo durante l'installazione.

### <a name="resolution"></a>Soluzione

Provare a installare l'estensione Log Analytics Agent per Windows quando la macchina virtuale ha un carico inferiore.

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato qui o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto tecnico di Azure connette la community di Azure a risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**.
