---
title: Risolvere i problemi relativi all'onboarding delle soluzioni di gestione di Automazione di AzureTroubleshoot onboarding Azure Automation
description: Informazioni su come risolvere problemi di onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c949556949e0c187d7c23c4dd32436e245bfbb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75889324"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Risolvere gli errori durante l'onboarding della gestione degli aggiornamenti, del rilevamento delle modifiche e dell'inventarioTroubleshoot errors when onboarding Update Management, Change Tracking, and Inventory

Durante l'onboarding di soluzioni quali Gestione aggiornamenti, Rilevamento modifiche e Inventario, è possibile riscontrare errori. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="known-issues"></a>Problemi noti

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Scenario: la ridenominazione di un nodo registrato richiede nuovamente l'annullamento/la registrazione

#### <a name="issue"></a>Problema

Un nodo viene registrato in Automazione di Azure e quindi il nome computer del sistema operativo viene modificato.  I report dal nodo continuano a essere visualizzati con il nome originale.

#### <a name="cause"></a>Causa

La ridenominazione dei nodi registrati non aggiorna il nome del nodo in Automazione di Azure.Renaming registered nodes does not update the node name in Azure Automation.

#### <a name="resolution"></a>Risoluzione

Annullare la registrazione del nodo da Configurazione stato di automazione di Azure e quindi registrarlo nuovamente.  I report pubblicati nel servizio prima di tale orario non saranno più disponibili.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: la firma di nuovi certificati tramite proxy https non è supportata

#### <a name="issue"></a>Problema

I clienti hanno segnalato che quando ci si connette tramite una soluzione proxy che termina il traffico https e quindi crittografa nuovamente il traffico utilizzando un nuovo certificato, il servizio non consente la connessione.

#### <a name="cause"></a>Causa

Automazione di Azure non supporta la firma dei certificati di firma nuovi usati per crittografare il traffico.

#### <a name="resolution"></a>Risoluzione

non è disponibile alcuna soluzione alternativa per questo problema.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: Onboarding non riuscito con il messaggio - Impossibile abilitare la soluzioneScenario: Onboarding fails with the message - The solution cannot be enabled

#### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi quando si tenta di eseguire l'onboarding di una macchina virtuale in una soluzione:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Questo errore è causato da autorizzazioni non corrette o mancanti nella macchina virtuale, nell'area di lavoro o per l'utente.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di disporre delle autorizzazioni corrette per eseguire l'onboarding della macchina virtuale. Verificare quali [autorizzazioni sono necessarie per eseguire l'onboarding delle macchine virtuali](../automation-role-based-access-control.md#onboarding) e ripetere l'operazione. Se viene visualizzato `The solution cannot be enabled on this VM because the permission to read the workspace is missing`l'errore `Microsoft.OperationalInsights/workspaces/read` , assicurarsi di disporre dell'autorizzazione per poter individuare se la macchina virtuale è stata attivata in un'area di lavoro.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: Onboarding non riesce con il messaggio - Impossibile configurare l'account di automazione per la registrazione diagnosticaScenario: Onboarding fails with the message - Failed to configure Automation Account for diagnostic logging

#### <a name="issue"></a>Problema

Quando si tenta di eseguire l'onboarding di una macchina virtuale in una soluzione, viene visualizzato il messaggio seguente:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Questo errore può essere causato se il piano tariffario non corrisponde al modello di fatturazione della sottoscrizione. Per altre informazioni, vedere [Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure.For](https://aka.ms/PricingTierWarning)more information, see Monitoring usage and estimated costs in Azure Monitor.

#### <a name="resolution"></a>Risoluzione

Creare manualmente l'area di lavoro di Log Analytics e ripetere il processo di onboarding per selezionare l'area di lavoro creata.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Questo codice di errore indica che il formato della query sul gruppo di computer oggetto della ricerca salvata usata per individuare la soluzione non è corretto. 

#### <a name="cause"></a>Causa

È possibile che la query sia stata modificata dall'utente o dal sistema.

#### <a name="resolution"></a>Risoluzione

È possibile eliminare la query per questa soluzione e caricare nuovamente la soluzione. In questo caso, la query viene creata di nuovo. Risulta impossibile trovare la query nel riquadro **Ricerche salvate** dell'area di lavoro. Il nome della query è **MicrosoftDefaultComputerGroup** e la categoria della query è il nome della soluzione associata alla query. Se sono abilitate più soluzioni, **MicrosoftDefaultComputerGroup** viene visualizzato più volte nell'area **Ricerche salvate**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problema

Questo codice di errore indica che la distribuzione non è riuscita a causa di una violazione di uno o più criteri.

#### <a name="cause"></a>Causa 

Vi sono criteri che impediscono il completamento dell'operazione.

#### <a name="resolution"></a>Risoluzione

Per poter distribuire la soluzione, è necessario provare a modificare i criteri indicati. È possibile definire vari tipi di criteri diversi. Le modifiche necessarie dipendono dal criterio violato. Ad esempio, se per un gruppo di risorse è stato definito un criterio che nega l'autorizzazione per modificare il contenuto di determinati tipi di risorse all'interno del gruppo, è possibile procedere in uno dei modi seguenti:

* Rimuovere completamente il criterio.
* Provare a eseguire il caricamento in un gruppo di risorse diverso.
* Rivedere i criteri, ad esempio:
  * Ridefinendo la destinazione dei criteri per una risorsa specifica (ad esempio un account di Automazione di Azure specifico).
  * Modificando il set di risorse per il quale il criterio specifico è stato configurato per negare l'autorizzazione.

Controllare le notifiche nell'angolo superiore destro del portale di Azure o passare al gruppo di risorse che contiene l'account di automazione e selezionare **Distribuzioni** in **Impostazioni** per visualizzare la distribuzione non riuscita. Per altre informazioni sui criteri di Azure, vedere [Informazioni su Criteri di Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: errori durante il tentativo di scollegare un'area di lavoroScenario: Errors trying to unlink a workspace

#### <a name="issue"></a>Problema

Viene visualizzato il seguente errore quando si tenta di scollegare un'area di lavoro:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando nell'area di lavoro di Log Analytics sono ancora attive soluzioni che dipendono dal collegamento dell'area di lavoro Account di automazione e Log Analytics.

### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, è necessario rimuovere le soluzioni seguenti dall'area di lavoro se vengono in uso:

* Gestione degli aggiornamenti
* Rilevamento modifiche
* Avviare/arrestare VM durante gli orari di minore attività

Dopo aver rimosso le soluzioni, è possibile scollegare l'area di lavoro. È importante pulire tutti gli elementi esistenti da tali soluzioni dall'area di lavoro e dall'account di automazione.  

* Gestione degli aggiornamenti
  * Rimuovere le distribuzioni di aggiornamento (pianificazioni) dall'account di automazione
* Avviare/arrestare VM durante gli orari di minore attività
  * Rimuovere eventuali blocchi sui componenti della soluzione nell'account di automazione in**Blocchi** **impostazioni** > .
  * Per altri passaggi per rimuovere le macchine virtuali di avvio/arresto durante la soluzione fuori orario, vedere Rimuovere la macchina virtuale di [avvio/arresto durante la soluzione fuori orario.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>Errori delle estensioni di MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Quando si distribuisce una soluzione, vengono distribuite anche varie risorse correlate. Una di queste risorse è l'estensione Microsoft Monitoring Agent o l'agente di Log Analytics per Linux. Si tratta di estensioni di macchine virtuali installate dall'agente guest della macchina virtuale responsabile della comunicazione con l'area di lavoro Log Analytics configurata, allo scopo di un successivo coordinamento del download dei file binari e di altri file soluzione che si sta onboarding dipendono una volta che inizia l'esecuzione.
In genere gli errori di installazione dell'estensione MMA o dell'agente di Log Analytics per Linux vengono notificati nell'Hub di notifica. Facendo clic sulla notifica vengono visualizzate altre informazioni sull'errore specifico. Si possono visualizzare altri dettagli sugli errori di distribuzione che si sono verificati anche accedendo alla risorsa Gruppi di risorse e quindi all'elemento Distribuzioni al suo interno.
L'installazione dell'estensione MMA o dell'agente di Log Analytics per Linux può non riuscire per diverse ragioni e la procedura per risolvere gli errori varia a seconda del problema. Di seguito vengono riportate le procedure per la risoluzione di problemi specifici.

Nella sezione seguente vengono descritti vari problemi che è possibile incontrare durante l'onboarding che causano un errore nella distribuzione dell'estensione MMA.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: è stata generata un'eccezione durante una richiesta WebClient

L'estensione MMA della macchina virtuale non può comunicare con le risorse esterne e la distribuzione non riesce.

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

* Nella macchina virtuale è configurato un proxy che consente solo porte specifiche.

* Un'impostazione del firewall ha bloccato l'accesso alle porte e agli indirizzi necessari.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di avere le porte e gli indirizzi aperti per la comunicazione. Per un elenco di porte e indirizzi, vedere [Planning your network](../automation-hybrid-runbook-worker.md#network-planning) (Pianificazione della rete).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: installazione non riuscita a causa di problemi temporanei dell'ambienteScenario: Install failed because of a transient environment issues

L'installazione dell'estensione di Microsoft Monitoring Agent non è riuscita durante la distribuzione a causa di un'altra installazione o azione che blocca l'installazione

#### <a name="issue"></a>Problema

Di seguito sono riportati esempi di messaggi di errore restituiti:

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

* È in corso un'altra installazione
* Il sistema viene attivato per il riavvio durante la distribuzione del modello

#### <a name="resolution"></a>Risoluzione

Si tratta di un problema di per sé temporaneo. Ritentare la distribuzione per installare l'estensione.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: timeout dell'installazione

L'installazione dell'estensione MMA non è stata completata a causa di un timeout.

#### <a name="issue"></a>Problema

L'esempio seguente è di un messaggio di errore che può essere restituito:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Questo errore si verifica perché la macchina virtuale è sotto un carico pesante durante l'installazione.

### <a name="resolution"></a>Risoluzione

Installare l'estensione MMA quando la macchina virtuale è sottoposta a un carico di lavoro più leggero.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
