---
title: Risoluzione degli errori di onboarding di Gestione aggiornamenti, Rilevamento modifiche e Inventario
description: Informazioni su come risolvere problemi di onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 52ff52ffb558278507bb24e1b1e2054c251b2512
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879641"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Risoluzione di problemi di onboarding delle soluzioni

Durante l'onboarding di soluzioni quali Gestione aggiornamenti, Rilevamento modifiche e Inventario, è possibile riscontrare errori. Questo articolo descrive i diversi errori che possono verificarsi e come risolverli.

## <a name="general-errors"></a>Errori generali

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

Controllare le notifiche nell'angolo inferiore destro del portale di Azure o passare al gruppo di risorse che contiene l'account di automazione e selezionare **Distribuzioni** in **Impostazioni** per visualizzare la distribuzione non riuscita. Per altre informazioni su Criteri di Azure, vedere: [Panoramica dei criteri di Azure](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Errori delle estensioni di MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Quando si distribuisce una soluzione, vengono distribuite anche varie risorse correlate. Una di queste risorse è l'estensione Microsoft Monitoring Agent o l'agente di Log Analytics per Linux. Si tratta di estensioni della macchina virtuale installate dall'agente guest della macchina virtuale responsabile della comunicazione con l'area di lavoro di Log Analytics configurata; lo scopo è coordinare successivamente il download di file binari e di altri file da cui dipende la soluzione di cui si sta eseguendo l'onboarding, una volta cominciata l'esecuzione.
In genere gli errori di installazione dell'estensione MMA o dell'agente di Log Analytics per Linux vengono notificati nell'Hub di notifica. Facendo clic sulla notifica vengono visualizzate altre informazioni sull'errore specifico. Si possono visualizzare altri dettagli sugli errori di distribuzione che si sono verificati anche accedendo alla risorsa Gruppi di risorse e quindi all'elemento Distribuzioni al suo interno.
L'installazione dell'estensione MMA o dell'agente di Log Analytics per Linux può non riuscire per diverse ragioni e la procedura per risolvere gli errori varia a seconda del problema. Di seguito vengono riportate le procedure per la risoluzione di problemi specifici.

Nella sezione seguente vengono descritti diversi problemi che possono verificarsi durante l'onboarding che causano errori nella distribuzione dell'estensione MMA.

### <a name="webclient-exception"></a>Scenario: È stata generata un'eccezione durante una richiesta WebClient

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

* Nella VM c'è un proxy configurato che consente solo porte specifiche.

* Un'impostazione del firewall ha bloccato l'accesso alle porte e agli indirizzi necessari.

#### <a name="resolution"></a>Risoluzione

Assicurarsi di avere le porte e gli indirizzi aperti per la comunicazione. Per un elenco di porte e indirizzi, vedere [Planning your network](../automation-hybrid-runbook-worker.md#network-planning) (Pianificazione della rete).

### <a name="transient-environment-issue"></a>Scenario: L'installazione non è riuscita a causa di problemi di ambiente temporanei

L'installazione dell'estensione Microsoft Monitoring Agent non è riuscita durante la distribuzione a causa di un'altra installazione o di un'azione che l'hanno bloccata

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
* È stato attivato il riavvio del sistema durante la distribuzione dei modelli

#### <a name="resolution"></a>Risoluzione

Si tratta di un problema di per sé temporaneo. Ritentare la distribuzione per installare l'estensione.

### <a name="installation-timeout"></a>Scenario: Timeout dell'installazione

L'installazione dell'estensione MMA non è stata completata a causa di un timeout.

#### <a name="issue"></a>Problema

Di seguito è riportato un esempio di un messaggio di errore che può essere restituito:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Causa

L'errore è causato dal fatto che la macchina virtuale è sottoposta a un carico di lavoro pesante durante l'installazione.

### <a name="resolution"></a>Risoluzione

Installare l'estensione MMA quando la macchina virtuale è sottoposta a un carico di lavoro più leggero.

## <a name="next-steps"></a>Passaggi successivi

Se il problema che si riscontra non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ricevere assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
