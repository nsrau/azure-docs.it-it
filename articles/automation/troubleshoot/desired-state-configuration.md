---
title: Risoluzione dei problemi della configurazione dello stato desiderato (DSC) di Automazione di Azure
description: Questo articolo contiene informazioni sulla risoluzione dei problemi di configurazione dello stato desiderato (DSC, Desired State Configuration)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8afa671a323e37a99be8b5a43d0a4823fe1877a
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58800877"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Risolvere i problemi della configurazione dello stato desiderato (DSC)

Questo articolo contiene informazioni sulla risoluzione dei problemi della configurazione dello stato desiderato (DSC, Desired State Configuration).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Errori comuni durante l'utilizzo di Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenario: Una configurazione con caratteri speciali non può essere eliminata dal portale

#### <a name="issue"></a>Problema

Quando si tenta di eliminare una configurazione DSC dal portale, viene visualizzato l'errore seguente:

```error
An error occured while deleteing the DSC configuration '<name>'.  Error-details: The arguement configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Questo errore è un problema temporaneo che è pianificato per essere risolto.

#### <a name="resolution"></a>Risoluzione

* Usare il Cmdlet di Az "Remove-AzAutomationDscConfiguration" per eliminare la configurazione.
* La documentazione relativa a questo cmdlet non è stata ancora aggiornata.  Fino ad allora, consultare la documentazione per il modulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-not-found"></a>Scenario: lo stato del nodo indica che non è riuscito con errore "Non trovato"

#### <a name="issue"></a>Problema

Un report del nodo indica lo stato **non riuscito** e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio ABC, anziché un nome di configurazione nodo, ad esempio ABC.WebServer.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi che si sta assegnando il nodo con "nome di configurazione nodo" e non la "configurazione name".
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Per assegnare una configurazione nodo a nodo mediante il portale di Azure, aprire il **nodi DSC** pagina, quindi selezionare un nodo e fare clic su **assegna configurazione nodo** pulsante.  
  * Per assegnare una configurazione nodo a nodo mediante il cmdlet di PowerShell, usare **Set-AzureRmAutomationDscNode** cmdlet

### <a name="no-mof-files"></a>Scenario: non sono state generate configurazioni di nodo (file con estensione mof) durante la compilazione di una configurazione

#### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando l'espressione che segue la parola chiave **Node** nella configurazione DSC restituisce `$null`, non vengono prodotte configurazioni di nodo.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Assicurarsi che l'espressione accanto al **nodo** parola chiave nella definizione della configurazione non è la valutazione degli $null.
* Se durante la compilazione della configurazione si passano dei dati di configurazione, verificare di specificare i valori previsti necessari per la configurazione da [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenario: il report relativo al nodo DSC rimane bloccato nello stato "In corso"

#### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

È stato eseguito l'aggiornamento della versione di WMF e WMI è stato danneggiato.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema, seguire le istruzioni riportate nel [limitazioni e problemi noti di DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) articolo.

### <a name="issue-using-credential"></a>Scenario: non è possibile usare le credenziali in una configurazione DSC

#### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore seguente:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Usa una credenziale in una configurazione ma non è stato corretto **ConfigurationData** per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di passare in appropriate **ConfigurationData** per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo che viene indicata nella configurazione. Per altre informazioni, vedere la sezione relativa agli [asset in Automation DSC per Azure](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
