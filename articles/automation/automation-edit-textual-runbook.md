---
title: Modificare runbook testuali in Automazione di Azure
description: Questo articolo illustra come usare l'editor di testo di Automazione di Azure per usare PowerShell e runbook del flusso di lavoro PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d388162738930030ba311a04a0dce1db15590c79
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836839"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Modificare runbook testuali in Automazione di Azure

È possibile usare l'editor di testo in Automazione di Azure per modificare i [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Questo editor ha le caratteristiche tipiche di altri editor di codice, ad esempio IntelliSense. Usa anche la codifica a colori con funzionalità speciali aggiuntive per semplificare l'accesso alle risorse comuni dei runbook. 

L'editor di testo include una funzionalità per inserire codice per cmdlet, risorse e runbook figlio all'interno di un runbook. Invece di digitare il codice manualmente, è possibile effettuare selezioni in un elenco di risorse disponibili; l'editor inserirà il codice nel runbook.

Ogni runbook in Automazione di Azure include due versioni, ovvero una versione bozza e una versione pubblicata. Si modifica la versione bozza del runbook e quindi lo si pubblica in modo da poterlo eseguire. La versione pubblicata non può essere modificata. Per altre informazioni, vedere [Pubblicare un runbook](manage-runbooks.md#publish-a-runbook).

In questo articolo vengono fornite procedure dettagliate per l'esecuzione delle diverse funzioni disponibili in questo editor. Queste procedure non sono applicabili ai [runbook grafici](automation-runbook-types.md#graphical-runbooks). Per usare questi runbook, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Modificare un runbook con il portale di Azure

1. Nel portale di Azure selezionare l'account di Automazione.
2. In **AUTOMAZIONE PROCESSI** selezionare **Runbook** per aprire l'elenco dei runbook.
3. Scegliere il runbook da modificare, quindi fare clic su **Modifica**.
4. Modificare il runbook.
5. Fare clic su **Salva** dopo aver completato le modifiche desiderate.
6. Fare clic su **Pubblica** se si vuole pubblicare la versione bozza più recente del runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Inserire un cmdlet in un runbook

1. Nell'area di disegno dell'editor di testo posizionare il cursore nel punto in cui si vuole inserire il cmdlet.
2. Espandere il nodo **Cmdlet** nel controllo della libreria.
3. Espandere il modulo contenente il cmdlet che si vuole usare.
4. Fare clic con il pulsante destro sul nome del cmdlet da inserire e scegliere **Aggiungi ad area di disegno**. Se il cmdlet include più di un set di parametri, l'editor aggiunge il set predefinito. È inoltre possibile espandere il cmdlet per selezionare un set di parametri diverso.
5. Si noti che il codice per il cmdlet viene inserito con l'intero elenco di parametri.
6. Fornire un valore appropriato al posto del valore racchiuso tra parentesi angolari (<>) per gli eventuali parametri obbligatori. Rimuovere i parametri non necessari.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Inserire il codice per un runbook figlio all'interno di un runbook

1. Nell'area di disegno dell'editor di testo posizionare il cursore nel punto in cui si vuole inserire il codice per il [runbook figlio](automation-child-runbooks.md).
2. Espandere il nodo **Runbook** nel controllo della libreria.
3. Fare clic con il pulsante destro sul runbook da inserire e selezionare **Aggiungi a canvas**.
4. Il codice relativo al runbook figlio viene inserito con qualsiasi segnaposto necessario per i parametri del runbook.
5. Sostituire i segnaposto con i valori appropriati per ogni parametro.

### <a name="insert-an-asset-into-a-runbook"></a>Inserire un asset in un runbook

1. Nell'area di disegno dell'editor di testo posizionare il cursore nel punto in cui si vuole inserire il codice per il runbook figlio.
2. Espandere il nodo **Asset** nel controllo della libreria.
3. Espandere il nodo per il tipo di asset desiderato.
4. Fare clic con il pulsante destro del mouse sul nome dell'asset da inserire e scegliere **Aggiungi ad area di disegno**. Per gli [asset di tipo variabile](automation-variables.md), selezionare l'opzione **Aggiungi "Ottieni variabile" all'area di disegno** o **Aggiungi "Imposta variabile" all'area di disegno** a seconda che si voglia ottenere o impostare la variabile.
5. Si noti che il codice per l'asset viene inserito nel runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Modificare un runbook di Automazione di Azure tramite Windows PowerShell

Per modificare un runbook con Windows PowerShell, usare l'editor desiderato e salvare il runbook in un file con estensione **PS1**. È possibile usare il cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) per recuperare il contenuto del runbook. È possibile usare il cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) per sostituire il runbook bozza esistente con quello modificato.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recuperare il contenuto di un runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come recuperare lo script per un runbook e salvarlo in un file di script. In questo esempio viene recuperata la versione bozza. È anche possibile recuperare la versione pubblicata del runbook, anche se questa versione non può essere modificata.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Modificare il contenuto di un runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come sostituire il contenuto di un runbook con il contenuto di un file di script. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

* [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* [Informazioni sul flusso di lavoro PowerShell](automation-powershell-workflow.md).
* [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
* [Certificati](automation-certificates.md).
* [Connessioni](automation-connections.md).
* [Credenziali](automation-credentials.md).
* [Pianificazioni](automation-schedules.md).
* [Variabili](automation-variables.md).
* [Informazioni di riferimento sui cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
