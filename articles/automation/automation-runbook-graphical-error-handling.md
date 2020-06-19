---
title: Gestire gli errori nei runbook grafici di Automazione di Azure
description: Questo articolo descrive come implementare la logica di gestione degli errori nei runbook grafici.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 26a4a3dbd54256fbc193fba299d0f7504f407254
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832266"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Gestire gli errori nei runbook grafici

Un principio di progettazione chiave da considerare per il runbook grafico di Automazione di Azure è l'identificazione dei problemi che potrebbero verificarsi durante l'esecuzione di runbook. ad esempio problemi relativi a esito positivo o negativo, stati di errore previsti e condizioni di errori imprevisti.

Spesso, se è presente un errore non irreversibile che si verifica con un'attività del runbook, Windows PowerShell gestisce l'attività elaborando eventuali attività successive, indipendentemente dall'errore. È probabile che l'errore generi un'eccezione, ma è comunque consentita l'esecuzione dell'attività successiva.

Il runbook grafico deve includere il codice di gestione degli errori per gestire i problemi di esecuzione. Per convalidare l'output di un'attività o gestire un errore, è possibile usare un'attività del codice di PowerShell, definire la logica condizionale nel collegamento di output dell'attività o applicare un altro metodo.

I runbook grafici di Automazione di Azure sono stati migliorati con la capacità di includere la gestione degli errori. È ora possibile trasformare le eccezioni in errori non irreversibili e creare collegamenti di errori tra le attività. Il processo migliorato consente al runbook di rilevare gli errori e gestire condizioni realizzate o impreviste. 

## <a name="powershell-error-types"></a>Tipi di errori di PowerShell

Durante l'esecuzione del runbook possono verificarsi errori PowerShell di tipo irreversibile o non irreversibile.
 
### <a name="terminating-error"></a>Errore irreversibile

Si tratta di un errore grave durante l'esecuzione, che arresta completamente un comando o l'esecuzione dello script. Gli esempi includono cmdlet inesistenti, errori di sintassi che impediscono l'esecuzione di un cmdlet e altri errori irreversibili.

### <a name="non-terminating-error"></a>Errore non irreversibile

Si tratta di un errore non grave che consente di continuare comunque l'esecuzione nonostante la condizione di errore. Gli esempi includono errori operativi come errori di file non trovati e problemi relativi alle autorizzazioni.

## <a name="when-to-use-error-handling"></a>Quando usare la gestione degli errori

Usare la gestione degli errori nel runbook quando un'attività critica genera un errore o un'eccezione. È importante impedire l'elaborazione dell'attività successiva del runbook e gestire l'errore in modo appropriato. La gestione dell'errore è essenziale soprattutto quando i runbook supportano un processo correlato alle operazioni aziendali o di servizio.

## <a name="add-error-links"></a>Aggiungere collegamenti di errore

Per ogni attività che può generare un errore, è possibile aggiungere un collegamento di errore che punta a qualsiasi altra attività. L'attività di destinazione può essere di qualsiasi tipo, incluse attività di codice, chiamata di un cmdlet, chiamata di un altro runbook e così via. L'attività di destinazione può avere anche collegamenti in uscita, regolari o di errore. I collegamenti consentono al runbook di implementare una logica complessa per la gestione degli errori senza dovere usare alcuna attività di codice.

La procedura consigliata comporta la creazione di un runbook dedicato per la gestione degli errori con funzionalità comuni, ma questa pratica non è obbligatoria. Si consideri ad esempio un runbook che prova ad avviare una macchina virtuale e installarvi un'applicazione. Se la macchina virtuale non viene avviata correttamente:

1. invia una notifica relativa al problema;
2. avvia un altro runbook che effettua automaticamente il provisioning di una nuova macchina virtuale.

Una soluzione consiste nell'avere un collegamento di errore nel runbook che fa riferimento a un'attività che gestisce il primo passaggio. Ad esempio, il runbook può connettere il cmdlet `Write-Warning` a un'attività per il passaggio due, ad esempio il cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0).

Si può anche generalizzare questo comportamento per usarlo in diversi runbook, inserendo le due attività in un runbook di gestione degli errori separato. Prima che il runbook originale chiami tale runbook di gestione degli errori, potrebbe creare un messaggio personalizzato dai suoi dati e quindi passarlo come parametro al runbook di gestione degli errori.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Trasformare le eccezioni in errori non irreversibili

Ogni attività del runbook ha un'impostazione di configurazione che trasforma le eccezioni in errori non irreversibili. Per impostazione predefinita, questa impostazione è disabilitata. Si consiglia di abilitare questa impostazione per qualsiasi attività in cui il runbook gestisce gli errori. Questa impostazione assicura che il runbook gestisca gli errori irreversibili e non irreversibili nell'attività come errori non irreversibili, usando un collegamento di errore.  

Dopo aver abilitato l'impostazione di configurazione, fare in modo che il runbook crei un'attività che gestisce l'errore. Se l'attività produce un errore, vengono seguiti i collegamenti all'errore prodotto. I collegamenti normali non vengono seguiti, sebbene l'attività produca anche un output regolare.<br><br> ![Esempio di collegamento di errore in un runbook di Automazione](media/automation-runbook-graphical-error-handling/error-link-example.png)

Nell'esempio seguente un runbook recupera una variabile che include il nome del computer di una macchina virtuale, quindi prova ad avviare la macchina virtuale con l'attività successiva.<br><br> ![Esempio di gestione degli errori in un runbook di Automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

L'attività `Get-AutomationVariable` e il cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) sono configurati in modo da convertire le eccezioni in errori. Se si verificano problemi nel recupero della variabile o nell'avvio della macchina virtuale, il codice genera errori.<br><br> ![Impostazioni delle attività di gestione degli errori in un runbook di Automazione](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png).

I collegamenti di errore passano da queste attività a una singola attività di codice `error management`. Questa attività viene configurata con una semplice espressione di PowerShell che usa la parola chiave `throw` per arrestare l'elaborazione, insieme a `$Error.Exception.Message` per ottenere il messaggio che illustra l'eccezione corrente.<br><br> ![Esempio di codice di gestione degli errori in un runbook di Automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione degli errori di runbook grafici, vedere [Risolvere gli errori di runbook](troubleshoot/runbooks.md).