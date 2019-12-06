---
title: Risoluzione degli errori di onboarding di Gestione aggiornamenti, Rilevamento modifiche e Inventario
description: Informazioni su come risolvere problemi di onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 0371c59ae63389bc3f7f0132260b0d98f496086c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849310"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Risoluzione di problemi di onboarding delle soluzioni

Durante l'onboarding di soluzioni quali Gestione aggiornamenti, Rilevamento modifiche e Inventario, è possibile riscontrare errori. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="known-issues"></a>Problemi noti

### <a name="node-rename"></a>Scenario: per rinominare un nodo registrato, è necessario annullare la registrazione o la registrazione

#### <a name="issue"></a>Problema

Un nodo viene registrato in automazione di Azure e quindi il sistema operativo ComputerName viene modificato.  I report del nodo continuano a essere visualizzati con il nome originale.

#### <a name="cause"></a>Causa

La ridenominazione dei nodi registrati non aggiorna il nome del nodo in automazione di Azure.

#### <a name="resolution"></a>Risoluzione

Annullare la registrazione del nodo dalla configurazione dello stato di automazione di Azure e quindi registrarlo di nuovo.  I report pubblicati nel servizio prima del momento non saranno più disponibili.


### <a name="resigning-cert"></a>Scenario: la nuova firma dei certificati tramite il proxy HTTPS non è supportata

#### <a name="issue"></a>Problema

I clienti hanno segnalato che, quando ci si connette tramite una soluzione proxy che termina il traffico HTTPS e quindi crittografa di nuovo il traffico tramite un nuovo certificato, il servizio non consente la connessione.

#### <a name="cause"></a>Causa

Automazione di Azure non supporta la ripetizione della firma dei certificati usati per crittografare il traffico.

#### <a name="resolution"></a>Risoluzione

non è disponibile alcuna soluzione alternativa per questo problema.

## <a name="general-errors"></a>Errori generali

### <a name="missing-write-permissions"></a>Scenario: l'onboarding ha esito negativo con il messaggio. Impossibile abilitare la soluzione

#### <a name="issue"></a>Problema

Quando si tenta di caricare una macchina virtuale in una soluzione, viene visualizzato uno dei messaggi seguenti:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Causa

Questo errore è causato da autorizzazioni errate o mancanti nella macchina virtuale, nell'area di lavoro o per l'utente.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di disporre delle autorizzazioni corrette per eseguire l'onboarding della macchina virtuale. Verificare quali [autorizzazioni sono necessarie per eseguire l'onboarding delle macchine virtuali](../automation-role-based-access-control.md#onboarding) e ripetere l'operazione. Se viene visualizzato il messaggio di errore `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, assicurarsi di disporre dell'autorizzazione `Microsoft.OperationalInsights/workspaces/read` per essere in grado di rilevare se la macchina virtuale è caricata in un'area di lavoro.

### <a name="diagnostic-logging"></a>Scenario: l'onboarding ha esito negativo con il messaggio-Impossibile configurare l'account di automazione per la registrazione diagnostica

#### <a name="issue"></a>Problema

Quando si tenta di eseguire l'onboarding di una macchina virtuale in una soluzione, viene visualizzato il messaggio seguente:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Causa

Questo errore può essere causato se il piano tariffario non corrisponde al modello di fatturazione della sottoscrizione. Per altre informazioni, vedere [monitoraggio dell'utilizzo e dei costi stimati in monitoraggio di Azure](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Risoluzione

Creare manualmente l'area di lavoro Log Analytics e ripetere il processo di onboarding per selezionare l'area di lavoro creata.

### <a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problema

Questo codice di errore indica che il formato della query sul gruppo di computer oggetto della ricerca salvata usata per individuare la soluzione non è corretto. 

#### <a name="cause"></a>Causa

È possibile che la query sia stata modificata dall'utente o dal sistema.

#### <a name="resolution"></a>Risoluzione

È possibile eliminare la query per questa soluzione e caricare nuovamente la soluzione. In questo caso, la query viene creata di nuovo. Risulta impossibile trovare la query nel riquadro **Ricerche salvate** dell'area di lavoro. Il nome della query è **MicrosoftDefaultComputerGroup** e la categoria della query è il nome della soluzione associata alla query. Se sono abilitate più soluzioni, **MicrosoftDefaultComputerGroup** viene visualizzato più volte nell'area **Ricerche salvate**.

### <a name="policy-violation"></a>Scenario: PolicyViolation

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

Controllare le notifiche nell'angolo superiore destro della portale di Azure o passare al gruppo di risorse contenente l'account di automazione e selezionare **distribuzioni** in **Impostazioni** per visualizzare la distribuzione non riuscita. Per altre informazioni sui criteri di Azure, vedere [Informazioni su Criteri di Azure](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Scenario: errori durante il tentativo di scollegare un'area di lavoro

#### <a name="issue"></a>Problema

Quando si tenta di scollegare un'area di lavoro, viene visualizzato l'errore seguente:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando le soluzioni sono ancora attive nell'area di lavoro di Log Analytics che dipendono dall'account di automazione e dall'area di lavoro di log analisi da collegare.

### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, è necessario rimuovere le soluzioni seguenti dall'area di lavoro se vengono usate:

* Gestione degli aggiornamenti
* Change Tracking
* Avviare/arrestare VM durante gli orari di minore attività

Una volta rimosse le soluzioni, è possibile scollegare l'area di lavoro. È importante pulire anche eventuali artefatti esistenti da tali soluzioni dall'area di lavoro e dall'account di automazione.  

* Gestione degli aggiornamenti
  * Rimuovere le distribuzioni di aggiornamenti (pianificazioni) dall'account di automazione
* Avviare/arrestare VM durante gli orari di minore attività
  * Rimuovere i blocchi sui componenti della soluzione nell'account di automazione in **impostazioni** > **blocchi**.
  * Per ulteriori passaggi per rimuovere la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività vedere, [rimuovere la soluzione avvia/arresta macchina virtuale durante gli orari](../automation-solution-vm-management.md##remove-the-solution)di indisponibilità.

## <a name="mma-extension-failures"></a>Errori delle estensioni di MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Quando si distribuisce una soluzione, vengono distribuite anche varie risorse correlate. Una di queste risorse è l'estensione Microsoft Monitoring Agent o l'agente di Log Analytics per Linux. Si tratta di estensioni di macchina virtuale installate dall'agente guest della macchina virtuale responsabile della comunicazione con l'area di lavoro configurata Log Analytics, allo scopo di un coordinamento successivo del download dei file binari e di altri file che il la soluzione da cui si sta eseguendo l'onboarding dipendono dall'avvio dell'esecuzione.
In genere gli errori di installazione dell'estensione MMA o dell'agente di Log Analytics per Linux vengono notificati nell'Hub di notifica. Facendo clic sulla notifica vengono visualizzate altre informazioni sull'errore specifico. Si possono visualizzare altri dettagli sugli errori di distribuzione che si sono verificati anche accedendo alla risorsa Gruppi di risorse e quindi all'elemento Distribuzioni al suo interno.
L'installazione dell'estensione MMA o dell'agente di Log Analytics per Linux può non riuscire per diverse ragioni e la procedura per risolvere gli errori varia a seconda del problema. Di seguito vengono riportate le procedure per la risoluzione di problemi specifici.

La sezione seguente descrive i vari problemi che è possibile riscontrare durante l'onboarding che provoca un errore nella distribuzione dell'estensione MMA.

### <a name="webclient-exception"></a>Scenario: è stata generata un'eccezione durante una richiesta WebClient

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

### <a name="transient-environment-issue"></a>Scenario: installazione non riuscita a causa di problemi di ambiente temporanei

L'installazione dell'estensione Microsoft Monitoring Agent non è riuscita durante la distribuzione a causa di un'altra installazione o azione che blocca l'installazione

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

### <a name="installation-timeout"></a>Scenario: timeout dell'installazione

L'installazione dell'estensione MMA non è stata completata a causa di un timeout.

#### <a name="issue"></a>Problema

Nell'esempio seguente viene visualizzato un messaggio di errore che può essere restituito:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

Questo errore si verifica perché la macchina virtuale è sottoposta a un carico eccessivo durante l'installazione.

### <a name="resolution"></a>Risoluzione

Installare l'estensione MMA quando la macchina virtuale è sottoposta a un carico di lavoro più leggero.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.
