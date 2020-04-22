---
title: Risoluzione dei problemi relativi all'onboarding delle soluzioni di gestione di Automazione di AzureTroubleshooting the onboarding of Azure Automation management solutions
description: Scopri come risolvere gli errori di onboarding della soluzione.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679223"
---
# <a name="troubleshoot-solution-onboarding"></a>Risolvere i problemi relativi all'onboarding della soluzioneTroubleshoot solution onboarding

È possibile che vengano visualizzati errori durante l'onboarding della soluzione di gestione degli aggiornamenti o della soluzione Rilevamento modifiche e inventario. In questo articolo vengono descritti i vari errori che potrebbero verificarsi e come risolverli.

## <a name="known-issues"></a>Problemi noti

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: la ridenominazione di un nodo registrato richiede nuovamente l'annullamento della registrazione o la registrazioneScenario: Renaming a registered node requires unregister or register again

#### <a name="issue"></a>Problema

Un nodo viene registrato in Automazione di Azure e quindi viene modificato il nome del computer del sistema operativo. I report dal nodo continuano a essere visualizzati con il nome originale.

#### <a name="cause"></a>Causa

La ridenominazione dei nodi registrati non aggiorna il nome del nodo in Automazione di Azure.Renaming registered nodes does not update the node name in Azure Automation.

#### <a name="resolution"></a>Risoluzione

Annullare la registrazione del nodo da Configurazione stato di automazione di Azure e quindi registrarlo nuovamente. I report pubblicati nel servizio prima di tale orario non saranno più disponibili.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: la firma di nuovi certificati tramite proxy https non è supportata

#### <a name="issue"></a>Problema

Quando ci si connette tramite una soluzione proxy che termina il traffico HTTPS e quindi crittografa nuovamente il traffico utilizzando un nuovo certificato, il servizio non consente la connessione.

#### <a name="cause"></a>Causa

Automazione di Azure non supporta la firma dei certificati di firma nuovi usati per crittografare il traffico.

#### <a name="resolution"></a>Risoluzione

Attualmente non esiste una soluzione per questo problema.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: Onboarding non riuscito con il messaggio - Impossibile abilitare la soluzioneScenario: Onboarding fails with the message - The solution cannot be enabled

#### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi quando si tenta di eseguire l'onboarding di una macchina virtuale a una soluzione:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Questo errore è causato da autorizzazioni non corrette o mancanti per la macchina virtuale o l'area di lavoro o per l'utente.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di disporre delle autorizzazioni corrette [necessarie per l'onboarding](../automation-role-based-access-control.md#onboarding-permissions) dei computer e quindi riprovare a eseguire l'aggiornamento della soluzione. Se viene visualizzato `The solution cannot be enabled on this VM because the permission to read the workspace is missing`l'errore , `Microsoft.OperationalInsights/workspaces/read` assicurarsi di disporre dell'autorizzazione per poter individuare se la macchina virtuale è stata caricata in un'area di lavoro.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: Onboarding non riesce con il messaggio: Impossibile configurare l'account di automazione per la registrazione diagnosticaScenario: Onboarding fails with the message: Failed to configure automation account for diagnostic logging

#### <a name="issue"></a>Problema

Viene visualizzato il messaggio seguente quando si tenta di eseguire l'onboarding di una macchina virtuale in una soluzione:You receive the following message when you attempt to onboard a VM to a solution:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Questo errore può essere causato se il piano tariffario non corrisponde al modello di fatturazione della sottoscrizione. Vedere [Monitoraggio dell'utilizzo e dei costi stimati in Monitoraggio di Azure.See Monitoring usage and estimated costs in Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Risoluzione

Creare manualmente l'area di lavoro di Log Analytics e ripetere il processo di onboarding per selezionare l'area di lavoro creata.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Questo codice di errore indica che la query del gruppo di computer di ricerca salvata utilizzata per la soluzione di destinazione non è formattata correttamente. 

#### <a name="cause"></a>Causa

È possibile che la query sia stata modificata o che il sistema l'abbia modificata.

#### <a name="resolution"></a>Risoluzione

È possibile eliminare la query per la soluzione e quindi eseguire nuovamente l'onboarding della soluzione, per cui viene ricreata la query. La query è disponibile nell'area di lavoro, in **Ricerche salvate**. Il nome della query è **MicrosoftDefaultComputerGroup**e la categoria della query è il nome della soluzione associata. Se sono abilitate più soluzioni, la query **MicrosoftDefaultComputerGroup** viene visualizzata più volte in **Ricerche salvate**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problema

Questo codice di errore indica che la distribuzione non è riuscita a causa di una violazione di uno o più criteri.

#### <a name="cause"></a>Causa 

Un criterio sta bloccando il completamento dell'operazione.

#### <a name="resolution"></a>Risoluzione

Per distribuire correttamente la soluzione, è necessario modificare i criteri indicati. Poiché è possibile definire molti tipi diversi di criteri, le modifiche necessarie dipendono dai criteri violati. Ad esempio, se un criterio è definito in un gruppo di risorse che nega l'autorizzazione per modificare il contenuto di alcune risorse contenute, è possibile scegliere una di queste correzioni:For example, if a policy is defined on a resource group that dens permission to change the contents of some contained resources, you might choose one of these fixes:

* Rimuovere completamente il criterio.
* Provare a eseguire l'onboarding della soluzione in un gruppo di risorse diverso.
* Reindirizzamento del criterio a una risorsa specifica, ad esempio un account di automazione.
* Modificare il set di risorse che il criterio è configurato per negare.

Controllare le notifiche nell'angolo superiore destro del portale di Azure o passare al gruppo di risorse che contiene l'account di automazione e selezionare **Distribuzioni** in **Impostazioni** per visualizzare la distribuzione non riuscita. Per altre informazioni sui criteri di Azure, vedere [Panoramica dei criteri di Azure.To](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)learn more about Azure Policy, see Overview of Azure Policy.

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: errori durante il tentativo di scollegare un'area di lavoroScenario: Errors trying to unlink a workspace

#### <a name="issue"></a>Problema

Viene visualizzato il seguente errore quando si tenta di scollegare un'area di lavoro:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando nell'area di lavoro di Log Analytics sono ancora attive soluzioni che dipendono dall'account di automazione e dall'area di lavoro di Log Analytics collegate.

### <a name="resolution"></a>Risoluzione

Se vengono create, rimuovere le soluzioni seguenti dall'area di lavoro:

* Gestione degli aggiornamenti
* Rilevamento modifiche e inventario
* Avviare/arrestare VM durante gli orari di minore attività

Dopo aver rimosso le soluzioni, è possibile scollegare l'area di lavoro. È importante pulire tutti gli elementi esistenti da queste soluzioni dall'area di lavoro e dall'account di automazione 

* Per Gestione aggiornamenti, rimuovere Distribuzioni di aggiornamenti (pianificazioni) dall'account di automazione.
* Per Avvia/Arresta macchine virtuali durante le ore non lavorative, rimuovere eventuali blocchi sui componenti della soluzione nell'account di automazione in**Blocchi** **impostazioni** > . Vedere Rimuovere le macchine virtuali di [avvio/arresto durante la soluzione fuori orario.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics per gli errori di estensione di WindowsLog Analytics for Windows extension failures

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Un'installazione dell'agente di Log Analytics per l'estensione di Windows può non riuscire per diversi motivi. Nella sezione seguente vengono descritti i problemi di onboarding che possono causare errori durante la distribuzione dell'agente di Log Analytics per l'estensione di Windows.

>[!NOTE]
>Agente di Log Analytics per Windows è il nome attualmente usato in Automazione di Azure per Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: è stata generata un'eccezione durante una richiesta WebClient

L'estensione Log Analytics per Windows nella macchina virtuale non è in grado di comunicare con le risorse esterne e la distribuzione non riesce.

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

Assicurarsi di avere le porte e gli indirizzi aperti per la comunicazione. Per un elenco di porte e indirizzi, vedere [Planning your network](../automation-hybrid-runbook-worker.md#network-planning) (Pianificazione della rete).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: installazione non riuscita a causa di problemi temporanei dell'ambienteScenario: Install failed because of a transient environment issues

L'installazione dell'estensione di Log Analytics per Windows non è riuscita durante la distribuzione a causa di un'altra installazione o azione che blocca l'installazione

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

* È in corso un'altra installazione.
* Il sistema viene attivato per riavviare durante la distribuzione del modello.

#### <a name="resolution"></a>Risoluzione

Questo errore è di natura transitoria. Ritentare la distribuzione per installare l'estensione.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: timeout dell'installazione

L'installazione dell'estensione di Log Analytics per Windows non è stata completata a causa di un timeout.

#### <a name="issue"></a>Problema

Di seguito è riportato un esempio di messaggio di errore che potrebbe essere restituito:The following is an example of an error message that might be returned:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Questo tipo di errore si verifica perché la macchina virtuale è sottoposta a un carico elevato durante l'installazione.

### <a name="resolution"></a>Risoluzione

Provare a installare l'agente di Log Analytics per l'estensione Windows quando la macchina virtuale è con un carico inferiore.

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema sopra o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
