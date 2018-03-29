---
title: Gestione degli errori nei runbook grafici di Automazione di Azure
description: Questo articolo descrive come implementare la logica di gestione degli errori nei runbook grafici di Automazione di Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9076178c75bd4d1669ecc8ab0119edb0119f85bd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Gestione degli errori nei runbook grafici di Automazione di Azure

Un elemento fondamentale della progettazione di un runbook è costituito dall'identificazione dei diversi problemi che potrebbero presentarsi in un runbook, ad esempio problemi relativi a esito positivo o negativo, stati di errore previsti e condizioni di errori imprevisti.

I runbook devono includere logica di gestione degli errori. Per convalidare l'output di un'attività o gestire un errore con runbook grafici, è possibile usare un'attività del codice di Windows PowerShell, definire la logica condizionale nel collegamento di output dell'attività o applicare un altro metodo.          

Se è presente un errore non irreversibile che si verifica con un'attività del runbook, eventuali attività successive vengono elaborate indipendentemente dall'errore. È probabile che l'errore generi un'eccezione, ma è comunque consentita l'esecuzione dell'attività successiva. PowerShell è stato progettato in questo modo per la gestione degli errori.    

Durante l'esecuzione possono verificarsi errori PowerShell di tipo fatale o non fatale. Ecco le differenze tra errori irreversibili e non irreversibili:

* **Errore irreversibile**: errore grave durante l'esecuzione, che arresta completamente il comando o l'esecuzione dello script. Gli esempi includono cmdlet inesistenti, errori di sintassi che impediscono l'esecuzione di un cmdlet o altri errori irreversibili.

* **Errore non irreversibili**: errore non grave che consente di continuare comunque l'esecuzione. Gli esempi includono errori operativi come errori di file non trovati e problemi relativi alle autorizzazioni.

I runbook grafici di Automazione di Azure sono stati migliorati con la capacità di includere la gestione degli errori. È ora possibile trasformare le eccezioni in errori non irreversibili e creare collegamenti di errori tra le attività. Questo processo consente a un autore di runbook di rilevare gli errori e gestire condizioni realizzate o impreviste.  

## <a name="when-to-use-error-handling"></a>Quando usare la gestione degli errori

Se si verifica un'attività critica che genera un'errore o un'eccezione, è importante evitare l'elaborazione dell'attività successiva del runbook e gestire l'errore in modo appropriato. Questo è essenziale soprattutto quando i runbook supportano un processo correlato alle operazioni aziendali o di servizio.

Per ogni attività che può generare un errore, l'autore del runbook può aggiungere un collegamento di errore che punta a qualsiasi altra attività. L'attività di destinazione può essere di qualsiasi tipo, incluse attività di codice, chiamata di un cmdlet, chiamata di un altro runbook e così via.

L'attività di destinazione può avere anche collegamenti in uscita, che possono essere collegamenti normali o collegamenti di errore. Ciò significa che l'autore del runbook può implementare logica complessa per la gestione degli errori senza dovere usare alcuna attività di codice. La procedura consigliata comporta la creazione di un runbook dedicato per la gestione degli errori con funzionalità comuni, ma questa operazione non è obbligatoria. La logica di gestione degli errori in un'attività di codice di PowerShell non è l'unica opzione.  

Si consideri ad esempio un runbook che prova ad avviare una macchina virtuale e installarvi un'applicazione. Se la macchina virtuale non viene avviata correttamente, vengono eseguite due azioni:

1. Invio di una notifica relativa al problema.
2. Avvio di un altro runbook che effettua automaticamente il provisioning di una nuova macchina virtuale.

Una soluzione consiste nell'avere un collegamento di errore che fa riferimento a un'attività che gestisce il primo passaggio. È ad esempio possibile connettere il cmdlet **Write-Warning** a un'attività per il passaggio due, ad esempio il cmdlet **Start-AzureRmAutomationRunbook**.

Si potrebbe anche generalizzare questo comportamento per usarlo in diversi runbook, inserendo le due attività in un runbook di gestione degli errori separato e seguendo le indicazioni riportate in precedenza. Prima di chiamare tale runbook di gestione degli errori, si potrebbe creare un messaggio personalizzato dai dati del runbook originale e quindi passarlo come parametro al runbook di gestione degli errori.

## <a name="how-to-use-error-handling"></a>Come usare la gestione degli errori

Ogni attività ha un'impostazione di configurazione che trasforma le eccezioni in errori non irreversibili. Per impostazione predefinita, questa impostazione è disabilitata. È consigliabile abilitare questa impostazione su qualsiasi attività in cui si vogliono gestire gli errori.  

Abilitando questa configurazione, gli errori sia irreversibili che non irreversibili nell'attività vengono trattati come errori non irreversibili e possono essere gestiti con un collegamento di errore.  

Dopo aver configurato tale impostazione, si creano le attività che gestiscono l'errore. Se un'attività genera un errore, vengono seguiti i collegamenti di errore in uscita e non i collegamenti regolari a prescindere dal fatto che l'attività abbia generato anche output regolare.<br><br> ![Esempio di collegamento di errore in un runbook di Automazione](media/automation-runbook-graphical-error-handling/error-link-example.png)

Nell'esempio seguente un runbook recupera una variabile che include il nome del computer di una macchina virtuale, quindi prova ad avviare la macchina virtuale con l'attività successiva.<br><br> ![Esempio di gestione degli errori in un runbook di Automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Le attività **Get-AutomationVariable** e **Start-AzureRmVm** sono configurate per convertire le eccezioni in errori. Se si verificano problemi nel recupero della variabile o nell'avvio della VM, vengono generati errori.<br><br> ![Impostazioni delle attività di gestione degli errori in un runbook di Automazione](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

I collegamenti di errore passano da queste attività a una singola attività di **gestione degli errori** (attività di codice). Questa attività viene configurata con una semplice espressione di PowerShell che usa la parola chiave *Throw* per arrestare l'elaborazione, insieme a *$Error.Exception.Message* per ottenere il messaggio che illustra l'eccezione corrente.<br><br> ![Esempio di codice di gestione degli errori in un runbook di Automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui collegamenti e sui tipi di collegamento di errore nei runbook grafici, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
