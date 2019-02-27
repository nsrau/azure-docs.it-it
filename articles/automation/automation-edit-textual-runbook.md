---
title: Modifica di runbook testuali in Automazione di Azure
description: In questo articolo vengono fornite procedure diverse per l'uso di PowerShell e dei runbook del flusso di lavoro PowerShell in Automazione di Azure mediante l'editor di testo.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9c684e9d1bf6cec12024cedfb5360d10e400e139
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416005"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Modifica di runbook testuali in Automazione di Azure

L'editor di testo in Automazione di Azure può essere usato per modificare i [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Questo editor dispone delle funzionalità tipiche degli altri editor di codice, ad esempio IntelliSense e la codifica a colori con particolari funzionalità aggiuntive per semplificare l'accesso alle risorse comuni dei runbook. In questo articolo vengono fornite procedure dettagliate per l'esecuzione delle diverse funzioni disponibili in questo editor.

L'editor di testo include una funzionalità per inserire codice per cmdlet, risorse e runbook figlio all'interno di un runbook. Invece di digitare il codice manualmente, è possibile effettuare selezioni in un elenco di risorse disponibili e inserire automaticamente il codice nel runbook.

Ogni runbook in Automazione di Azure include due versioni, ovvero una versione bozza e una versione pubblicata. Si modifica la versione bozza del runbook e quindi lo si pubblica in modo da poterlo eseguire. La versione pubblicata non può essere modificata. Per altre informazioni, vedere [Pubblicazione di un Runbook](manage-runbooks.md#publish-a-runbook).

Per usare i [runbook grafici](automation-runbook-types.md#graphical-runbooks), vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Per modificare un runbook con il portale di Azure

Usare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nel portale di Azure selezionare l'account di automazione.
2. In **AUTOMAZIONE PROCESSI** selezionare **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook che si vuole modificare e quindi fare clic sul pulsante **Modifica**.
4. Modificare il runbook.
5. Fare clic su **Salva** dopo aver completato le modifiche desiderate.
6. Fare clic su **Pubblica** se si desidera pubblicare la versione bozza più recente del runbook.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Per inserire un cmdlet in un runbook

1. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il cmdlet.
2. Espandere il nodo **Cmdlet** nel controllo della libreria.
3. Espandere il modulo contenente il cmdlet che si desidera usare.
4. Fare clic con il pulsante destro sul cmdlet da inserire e selezionare **Aggiungi a canvas**. Se il cmdlet include più di un set di parametri, verrà aggiunto il set predefinito. È inoltre possibile espandere il cmdlet per selezionare un set di parametri diverso.
5. Il codice per il cmdlet viene inserito con l'intero elenco di parametri.
6. Fornire un valore appropriato al posto del tipo di dati racchiuso tra parentesi graffe <> per gli eventuali parametri obbligatori. Rimuovere i parametri non necessari.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Per inserire il codice per un runbook figlio all'interno di un runbook

1. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il [runbook figlio](automation-child-runbooks.md).
2. Espandere il nodo **Runbook** nel controllo della libreria.
3. Fare clic con il pulsante destro sul runbook da inserire e selezionare **Aggiungi a canvas**.
4. Il codice relativo al runbook figlio viene inserito con qualsiasi segnaposto necessario per i parametri del runbook.
5. Sostituire i segnaposto con i valori appropriati per ogni parametro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Per inserire un asset in un runbook

1. Nel canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il runbook figlio.
2. Espandere il nodo **Asset** nel controllo della libreria.
3. Espandere il nodo per il tipo di asset desiderato.
4. Fare clic con il pulsante destro sull'asset da inserire e selezionare **Aggiungi a canvas**. Per gli [asset di tipo variabile](automation-variables.md), selezionare l'opzione **Aggiungi "Ottieni variabile" a canvas** o **Aggiungi "Imposta variabile" a canvas** a seconda che si desideri ottenere o impostare la variabile.
5. Il codice per l'asset viene inserito nel runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Per modificare un runbook di Automazione di Azure tramite Windows PowerShell

Per modificare un runbook con Windows PowerShell, usare l'editor desiderato e salvarlo in un file con estensione `.ps1`. È possibile usare il cmdlet [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) per recuperare il contenuto del runbook e quindi il cmdlet [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) per sostituire la versione bozza del runbook esistente con quello modificato.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Per recuperare il contenuto di un runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come recuperare lo script per un runbook e salvarlo in un file di script. In questo esempio viene recuperata la versione bozza. È possibile anche recuperare la versione pubblicata del runbook, sebbene questa versione non possa essere modificata.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Per modificare il contenuto di un Runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come sostituire il contenuto di un runbook con il contenuto di un file di script. Questa è la stessa procedura di esempio descritta in [Per importare un runbook da un file di script con Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Articoli correlati

* [Gestire runbook in Automazione di Azure](manage-runbooks.md)
* [Informazioni sul flusso di lavoro PowerShell](automation-powershell-workflow.md)
* [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
* [Certificati](automation-certificates.md)
* [Connessioni](automation-connections.md)
* [Credenziali](automation-credentials.md)
* [Pianificazioni](automation-schedules.md)
* [Variabili](automation-variables.md)

