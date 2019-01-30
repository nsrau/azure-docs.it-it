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
ms.openlocfilehash: 997f332e14fd1accf32d8cc3f51557fe005acab5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421646"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Risolvere i problemi della configurazione dello stato desiderato (DSC)

Questo articolo contiene informazioni sulla risoluzione dei problemi della configurazione dello stato desiderato (DSC, Desired State Configuration).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Errori comuni durante l'utilizzo di Desired State Configuration (DSC)

### <a name="failed-not-found"></a>Scenario: lo stato del nodo indica che non è riuscito con errore "Non trovato"

#### <a name="issue"></a>Problema

Un report del nodo indica lo stato **non riuscito** e contiene l'errore seguente:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio ABC, anziché un nome di configurazione nodo, ad esempio ABC.WebServer.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare al nodo un "nome di configurazione di nodo" e non un "nome di configurazione".
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Per assegnare a un nodo una configurazione nodo mediante il portale di Azure, aprire il riquadro **Nodi DSC**, selezionare un nodo e quindi fare clic sul pulsante **Assegna configurazione nodo**.  
  * Per assegnare a un nodo una configurazione nodo mediante PowerShell, usare il cmdlet **Set-AzureRmAutomationDscNode** .

### <a name="no-mof-files"></a>Scenario: non sono state generate configurazioni di nodo (file con estensione mof) durante la compilazione di una configurazione

#### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando l'espressione che segue la parola chiave **Node** nella configurazione DSC restituisce `$null`, non vengono prodotte configurazioni di nodo.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Verificare che l'espressione accanto alla parola chiave **Node** nella definizione di configurazione non restituisca $null.
* Se durante la compilazione della configurazione si passano dei dati di configurazione, verificare di specificare i valori previsti necessari per la configurazione da [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenario: il report relativo al nodo DSC rimane bloccato nello stato "In corso"

#### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```
No instance found with given property values
```

#### <a name="cause"></a>Causa

È stato eseguito l'aggiornamento della versione di WMF e WMI è stato danneggiato.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema, seguire le istruzioni riportate nell'articolo [Limitazioni e problemi noti di DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc).

### <a name="issue-using-credential"></a>Scenario: non è possibile usare le credenziali in una configurazione DSC

#### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore seguente:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

In una configurazione sono state usate credenziali, ma non è stato passato l'oggetto **ConfigurationData** corretto per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo.

#### <a name="resolution"></a>Risoluzione

* Verificare di passare l'oggetto **ConfigurationData** corretto per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo indicata nella configurazione. Per altre informazioni, vedere la sezione relativa agli [asset in Automation DSC per Azure](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Passaggi successivi

Se il problema che si riscontra non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ricevere assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
