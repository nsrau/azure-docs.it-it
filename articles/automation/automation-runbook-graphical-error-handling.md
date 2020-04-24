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

Un principio di progettazione chiave da considerare per il Runbook grafico di automazione di Azure è l'identificazione dei problemi che potrebbero verificarsi durante l'esecuzione di Runbook. ad esempio problemi relativi a esito positivo o negativo, stati di errore previsti e condizioni di errori imprevisti.

Spesso, in caso di errore non fatale che si verifica con un'attività Runbook, Windows PowerShell gestisce l'attività elaborando qualsiasi attività successiva, indipendentemente dall'errore. È probabile che l'errore generi un'eccezione, ma è comunque consentita l'esecuzione dell'attività successiva.

Il Runbook grafico deve includere il codice di gestione degli errori per gestire i problemi di esecuzione. Per convalidare l'output di un'attività o gestire un errore, è possibile usare un'attività di codice PowerShell, definire la logica condizionale nel collegamento di output dell'attività oppure applicare un altro metodo.

I runbook grafici di Automazione di Azure sono stati migliorati con la capacità di includere la gestione degli errori. È ora possibile trasformare le eccezioni in errori non irreversibili e creare collegamenti di errori tra le attività. Il processo migliorato consente ai Runbook di rilevare gli errori e di gestire le condizioni realizzate o impreviste. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Tipi di errore di PowerShell

I tipi di errori di PowerShell che possono verificarsi durante l'esecuzione di Runbook sono la chiusura di errori e errori non fatali.
 
### <a name="terminating-error"></a>Errore di terminazione

Un errore irreversibile è un errore grave durante l'esecuzione che interrompe completamente l'esecuzione di un comando o di uno script. Gli esempi includono cmdlet inesistenti, errori di sintassi che impediscono l'esecuzione di un cmdlet e altri errori irreversibili.

### <a name="non-terminating-error"></a>Errore non fatale

Un errore non fatale è un errore non grave che consente di continuare l'esecuzione nonostante la condizione di errore. Gli esempi includono errori operativi, ad esempio errori di file non trovati e problemi di autorizzazioni.

## <a name="when-to-use-error-handling"></a>Quando usare la gestione degli errori

Usare la gestione degli errori in runbook quando un'attività critica genera un errore o un'eccezione. È importante impedire l'elaborazione dell'attività successiva del Runbook e gestire l'errore in modo appropriato. La gestione dell'errore è particolarmente importante quando i manuali operativi supportano un processo operativo aziendale o del servizio.

Per ogni attività che può generare un errore, è possibile aggiungere un collegamento di errore che punta a qualsiasi altra attività. L'attività di destinazione può essere di qualsiasi tipo, tra cui attività del codice, chiamata di un cmdlet, chiamata di un altro Runbook e così via. L'attività di destinazione può inoltre includere collegamenti in uscita, ovvero collegamenti normali o di errore. I collegamenti consentono all'Runbook di implementare una logica di gestione degli errori complessa senza ricorrere a un'attività di codice.

La procedura consigliata consiste nel creare un Runbook di gestione degli errori dedicato con funzionalità comuni, ma questa pratica non è obbligatoria. Si consideri, ad esempio, un Runbook che tenta di avviare una macchina virtuale e di installarvi un'applicazione. Se la macchina virtuale non viene avviata correttamente,:

1. Invia una notifica relativa a questo problema.
2. Avvia un altro Runbook che effettua automaticamente il provisioning di una nuova macchina virtuale.

Una soluzione consiste nel disporre di un collegamento di errore in Runbook che punta a un'attività che gestisce il passaggio uno. Ad esempio, Runbook può connettere il `Write-Warning` cmdlet a un'attività per il passaggio due, ad esempio il cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) .

È anche possibile generalizzare questo comportamento per l'uso in molti manuali operativi inserendo queste due attività in un Runbook di gestione degli errori separato. Prima che il Runbook originale chiami questo errore di gestione Runbook, può costruire un messaggio personalizzato dai dati e quindi passarlo come parametro al Runbook di gestione degli errori.

## <a name="how-to-use-error-handling"></a>Come usare la gestione degli errori

Ogni attività nel runbook dispone di un'impostazione di configurazione che consente di trasformare le eccezioni in errori non fatali. Per impostazione predefinita, questa impostazione è disabilitata. Si consiglia di abilitare questa impostazione per qualsiasi attività in cui il Runbook gestisce gli errori. Questa impostazione assicura che Runbook gestisca gli errori di terminazione e non fatale nell'attività come errori non fatali, usando un collegamento di errore.  

Dopo aver abilitato l'impostazione di configurazione, fare in modo che il Runbook crei un'attività che gestisce l'errore. Se l'attività genera un errore, vengono seguiti i collegamenti di errore in uscita. I collegamenti normali non vengono seguiti, anche se l'attività produce anche un output regolare.<br><br> ![Esempio di collegamento di errore in un runbook di Automazione](media/automation-runbook-graphical-error-handling/error-link-example.png)

Nell'esempio seguente un Runbook recupera una variabile che contiene il nome computer di una macchina virtuale. Tenta quindi di avviare la macchina virtuale con l'attività successiva.<br><br> ![Esempio di gestione degli errori di Runbook di automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

L' `Get-AutomationVariable` attività e il cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) sono configurati in modo da convertire le eccezioni in errori. Se si verificano problemi durante il recupero della variabile o l'avvio della macchina virtuale, il codice genera errori.<br><br> ![Impostazioni](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)delle attività di gestione degli errori di Runbook di automazione.

I collegamenti di errore propagano da queste `error management` attività a una singola attività di codice. Questa attività viene configurata con una semplice espressione PowerShell che `throw` usa la parola chiave per arrestare l' `$Error.Exception.Message` elaborazione, insieme a per ottenere il messaggio che descrive l'eccezione corrente.<br><br> ![Esempio di codice di gestione degli errori Runbook di automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui collegamenti e sui tipi di collegamento di errore nei runbook grafici, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Per altre informazioni sull'esecuzione di Runbook, sul monitoraggio dei processi Runbook e su altri dettagli tecnici, vedere [esecuzione di Runbook in automazione di Azure](automation-runbook-execution.md).