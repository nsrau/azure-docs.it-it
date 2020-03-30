---
title: Gestione degli errori nei runbook grafici di Automazione di Azure
description: Questo articolo descrive come implementare la logica di gestione degli errori nei runbook grafici di Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367075"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Gestione degli errori nei runbook grafici di Automazione di Azure

Un principio di progettazione chiave da considerare per il runbook grafico di Automazione di Azure è l'identificazione dei problemi che il runbook potrebbe verificarsi durante l'esecuzione. ad esempio problemi relativi a esito positivo o negativo, stati di errore previsti e condizioni di errori imprevisti.

Spesso, se si verifica un errore non di terminazione che si verifica con un'attività del runbook, Windows PowerShell gestisce l'attività elaborando qualsiasi attività che segue, indipendentemente dall'errore. È probabile che l'errore generi un'eccezione, ma è comunque consentita l'esecuzione dell'attività successiva.

Il runbook grafico deve includere il codice di gestione degli errori per gestire i problemi di esecuzione. Per convalidare l'output di un'attività o gestire un errore, è possibile usare un'attività di codice di PowerShell, definire la logica condizionale nel collegamento di output dell'attività o applicare un altro metodo.

I runbook grafici di Automazione di Azure sono stati migliorati con la capacità di includere la gestione degli errori. È ora possibile trasformare le eccezioni in errori non irreversibili e creare collegamenti di errori tra le attività. Il processo migliorato consente al runbook di rilevare gli errori e gestire le condizioni realizzate o impreviste. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="powershell-error-types"></a>Tipi di errore di PowerShellPowerShell error types

I tipi di errori di PowerShell che possono verificarsi durante l'esecuzione del runbook sono gli errori di terminazione e gli errori non di terminazione.
 
### <a name="terminating-error"></a>Errore di terminazione

Un errore di terminazione è un errore grave durante l'esecuzione che interrompe completamente l'esecuzione di un comando o di uno script. Gli esempi includono cmdlet inesistenti, errori di sintassi che impediscono l'esecuzione di un cmdlet e altri errori irreversibili.

### <a name="non-terminating-error"></a>Errore non di terminazione

Un errore non di terminazione è un errore non grave che consente l'esecuzione di continuare nonostante la condizione di errore. Gli esempi includono errori operativi, ad esempio errori di file non rilevati e problemi di autorizzazioni.

## <a name="when-to-use-error-handling"></a>Quando usare la gestione degli errori

Utilizzare la gestione degli errori nel runbook quando un'attività critica genera un errore o un'eccezione. È importante impedire l'elaborazione dell'attività successiva nel runbook e gestire l'errore in modo appropriato. La gestione dell'errore è particolarmente importante quando i runbook supportano un processo di operazioni aziendali o di servizio.

Per ogni attività che può generare un errore, è possibile aggiungere un collegamento di errore che punta a qualsiasi altra attività. L'attività di destinazione può essere di qualsiasi tipo, tra cui l'attività del codice, la chiamata di un cmdlet, la chiamata di un altro runbook e così via. L'attività di destinazione può anche avere collegamenti in uscita, collegamenti regolari o di errore. I collegamenti consentono al runbook di implementare una logica complessa di gestione degli errori senza ricorrere a un'attività di codice.

La procedura consigliata consiste nel creare un runbook dedicato per la gestione degli errori con funzionalità comuni, ma questa procedura non è obbligatoria. Si consideri, ad esempio, un runbook che tenta di avviare una macchina virtuale e installare un'applicazione su di essa. Se la macchina virtuale non viene avviata correttamente, è necessario:If the VM doesn't start correctly, it:

1. Invia una notifica su questo problema.
2. Avvia un altro runbook che esegue automaticamente il provisioning di una nuova macchina virtuale.

Una soluzione consiste nel fare in modo che nel runbook sia presente un collegamento di errore che punta a un'attività che gestisce il passaggio uno. Ad esempio, il runbook `Write-Warning` può connettere il cmdlet a un'attività per il passaggio due, ad esempio il cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

È anche possibile generalizzare questo comportamento per l'uso in molti runbook inserendo queste due attività in un runbook separato per la gestione degli errori. Prima che il runbook originale chiami questo runbook di gestione degli errori, può costruire un messaggio personalizzato dai relativi dati e quindi passarlo come parametro al runbook di gestione degli errori.

## <a name="how-to-use-error-handling"></a>Come usare la gestione degli errori

Ogni attività nel runbook ha un'impostazione di configurazione che trasforma le eccezioni in errori non di terminazione. Per impostazione predefinita, questa impostazione è disabilitata. Ti consigliamo di abilitare questa impostazione su qualsiasi attività in cui il runbook gestisce gli errori. Questa impostazione assicura che il runbook gestisca gli errori di terminazione e di non terminazione nell'attività come errori non di terminazione, utilizzando un collegamento di errore.  

Dopo aver abilitato l'impostazione di configurazione, fare in modo che il runbook crei un'attività che gestisce l'errore. Se l'attività genera un errore, vengono seguiti i collegamenti degli errori in uscita. I collegamenti regolari non vengono seguiti, anche se l'attività produce anche un output regolare.<br><br> ![Esempio di collegamento di errore in un runbook di Automazione](media/automation-runbook-graphical-error-handling/error-link-example.png)

Nell'esempio seguente, un runbook recupera una variabile che contiene il nome computer di una macchina virtuale. Tenta quindi di avviare la macchina virtuale con l'attività successiva.<br><br> ![Esempio di gestione degli errori del runbook di automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

L'attività `Get-AutomationVariable` e il cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) sono configurati per convertire le eccezioni in errori. Se si verificano problemi durante il recupero della variabile o l'avvio della macchina virtuale, il codice genera errori.<br><br> ![Impostazioni dell'attività di](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)gestione degli errori del runbook di automazione .

I collegamenti di errore passano da queste attività a una singola `error management` attività di codice. Questa attività è configurata con una `throw` semplice espressione di PowerShell che usa la parola chiave per interrompere l'elaborazione, insieme `$Error.Exception.Message` a ottenere il messaggio che descrive l'eccezione corrente.<br><br> ![Esempio di codice di gestione degli errori del runbook di automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui collegamenti e sui tipi di collegamento di errore nei runbook grafici, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Per altre informazioni sull'esecuzione del runbook, sul monitoraggio dei processi del runbook e altri dettagli tecnici, vedere Esecuzione di Runbook in Automazione di Azure.To learn more about runbook execution, monitoring of runbook jobs, and other technical details, [see Runbook execution in Azure Automation.](automation-runbook-execution.md)