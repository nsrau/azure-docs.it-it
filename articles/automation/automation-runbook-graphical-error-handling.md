---
title: Gestione degli errori nei runbook grafici di Automazione di Azure | Documentazione Microsoft
description: Questo articolo descrive come implementare la logica di gestione degli errori nei runbook grafici di Automazione di Azure.
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Gestione degli errori nei runbook grafici di Automazione di Azure

Un principio chiave da considerare nella progettazione dei runbook è l'identificazione dei diversi problemi che possono verificarsi in un runbook, includendo esito positivo, stati di errore previsti e condizioni di errore impreviste.  I runbook devono includere logica di gestione degli errori per eseguire il rilevamento di conseguenza.  Con i runbook grafici, per convalidare l'output di un'attività o gestire un errore in modo appropriato è possibile eseguire un'attività di codice PowerShell, definire logica condizionale nel collegamento dell'output dell'attività oppure applicare un altro metodo.          

Se durante l'esecuzione dei runbook si verifica un errore non fatale in un'attività, spesso le attività successive vengono comunque elaborate.  Verrà naturalmente generata un'eccezione, ma l'attività successiva potrà essere eseguita. Il motivo alla base di questo comportamento risiede nel modo in cui il linguaggio PowerShell è progettato per gestire gli errori.    

Durante l'esecuzione possono verificarsi errori PowerShell di tipo fatale o non fatale.  Di seguito è illustrata la differenza.

* Errore fatale: errore grave durante l'esecuzione che interrompe completamente il comando o l'esecuzione di script. Gli esempi possono includere cmdlet inesistenti, errori di sintassi che impedirebbero l'esecuzione di un cmdlet o altri errori irreversibili.

* Errore non fatale: errore non grave che consente di continuare comunque l'esecuzione. Gli esempi includono errori operativi come file non trovati, problemi relativi alle autorizzazioni e così via.

I runbook grafici di Automazione di Azure sono stati migliorati offrendo la possibilità di includere la gestione degli errori in modo da trasformare le eccezioni in errori non fatali e creare collegamenti di errore tra le attività. In questo modo, l'autore di un runbook può rilevare gli errori e usare un percorso per gestire la condizione realizzata o imprevista.  

## <a name="when-to-use"></a>Quando usare le autorizzazioni

Controllare l'esecuzione del flusso di lavoro è importante per poter impedire il passaggio all'attività successiva del runbook e gestire l'errore in modo appropriato quando un'attività critica genera un errore o un'eccezione.  Questo è necessario soprattutto quando i runbook supportano un processo correlato alle operazioni aziendali o di servizio.

Per ogni attività che potrebbe generare un errore, l'autore del runbook può aggiungere un collegamento di errore che punta a qualsiasi altra attività.  L'attività di destinazione può essere di qualsiasi tipo: attività di codice, chiamata di un cmdlet, chiamata di un altro runbook o altro. 

L'attività di destinazione può anche contenere collegamenti in uscita, costituiti da collegamenti regolari o di errore. In questo modo, l'autore del runbook può implementare logica di gestione degli errori complessa senza dover includere un'attività di codice.  Nonostante sia consigliabile creare un runbook di gestione degli errori dedicato con funzionalità comuni, questa procedura non è obbligatoria e l'inclusione della logica di gestione degli errori in un'attività di codice PowerShell non è l'unica alternativa.  

Si consideri ad esempio un runbook che prova ad avviare una VM e installarvi un'applicazione, ma che se la VM non viene avviata correttamente esegue le due azioni seguenti: 

1. Invia una notifica relativa al problema.
2. Avvia un altro runbook che effettua automaticamente il provisioning di una nuova VM. 

Una soluzione consisterebbe nell'includere un collegamento di errore che punta a un'attività per la gestione del passaggio 1, come il cmdlet **Write-Warning**, connessa a un'attività per il passaggio 2, come il cmdlet **Start-AzureRmAutomationRunbook**. 

Si potrebbe anche generalizzare questo comportamento per usarlo in diversi runbook e inserire le due attività in un runbook di gestione degli errori separato, seguendo le indicazioni riportate in precedenza.  Prima di chiamare tale runbook di gestione degli errori, si potrebbe creare un messaggio personalizzato dai dati del runbook originale e quindi passare tale messaggio come parametro al runbook di gestione degli errori. 

## <a name="how-to-use"></a>Utilizzo

Ogni attività ha una configurazione che trasforma le eccezioni in errori non fatali. Per impostazione predefinita, tale configurazione è disabilitata.  È consigliabile abilitarla in tutte le attività in cui si vogliono gestire gli errori.  Abilitando questa configurazione, gli errori sia fatali che non fatali nell'attività verranno trattati come errori non fatali e potranno quindi gestiti con un collegamento di errore.  Dopo aver configurato tale impostazione, si creano le attività che gestiranno l'errore.  Se un'attività genera un errore, verranno seguiti i collegamenti di errore in uscita e non i collegamenti regolari a prescindere dal fatto che l'attività abbia generato anche output regolare.<br><br> ![Esempio di collegamento di errore in un runbook di automazione](media/automation-runbook-graphical-error-handling/error-link-example.png)

Nel semplice esempio seguente, un runbook recupera una variabile contenente il nome computer di una macchina virtuale e quindi tenta di avviare la macchina virtuale con l'attività successiva.<br><br> ![Esempio di gestione degli errori in un runbook di automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Le attività **Get-AutomationVariable** e **Start-AzureRmVm** sono configurate per convertire le eccezioni in errori.  Se si verificano problemi nel recupero della variabile o nell'avvio della VM, verranno generati errori.<br><br> ![Impostazioni delle attività di gestione degli errori in un runbook di automazione](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

I collegamenti di errore passano da queste attività a una singola attività di codice di **gestione degli errori** configurata con una semplice espressione PowerShell che usa la parola chiave *Throw* per interrompere l'elaborazione e *$Error.Exception.Message* per recuperare il messaggio che descrive l'eccezione corrente.<br><br> ![Esempio di codice di gestione degli errori in un runbook di automazione](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui collegamenti e sui tipi di collegamento di errore nei runbook grafici, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md#links-and-workflow).

* Per maggiori informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere come tenere traccia del processo di un runbook in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md) 


<!--HONumber=Jan17_HO1-->


