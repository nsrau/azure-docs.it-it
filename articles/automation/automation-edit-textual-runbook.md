---
title: Modifica di runbook testuali in Automazione di Azure
description: In questo articolo vengono fornite procedure diverse per l'uso di PowerShell e dei runbook del flusso di lavoro PowerShell in Automazione di Azure mediante l'editor di testo.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3bbfe26f336a25ee85f2223226d6eb513ae21736
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632153"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Modifica di runbook testuali in Automazione di Azure

L'editor di testo in Automazione di Azure può essere usato per modificare i [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Questo editor ha le funzionalità tipiche di altri editor di codice, ad esempio IntelliSense.This editor has the typical features of other code editors, such as IntelliSense. Ha anche codifica a colori con funzionalità speciali aggiuntive per aiutarti ad accedere alle risorse comuni ai runbook. 

L'editor di testo include una funzionalità per inserire codice per cmdlet, risorse e runbook figlio all'interno di un runbook. Anziché digitare il codice manualmente, è possibile selezionare da un elenco di risorse disponibili e l'editor inserisce il codice appropriato nel runbook.

Ogni runbook in Automazione di Azure include due versioni, ovvero una versione bozza e una versione pubblicata. Si modifica la versione bozza del runbook e quindi lo si pubblica in modo da poterlo eseguire. La versione pubblicata non può essere modificata. Per ulteriori informazioni, vedere [Pubblicazione di un runbook](manage-runbooks.md#publishing-a-runbook).

In questo articolo vengono fornite procedure dettagliate per l'esecuzione delle diverse funzioni disponibili in questo editor. Questi non sono applicabili [ai runbook grafici.](automation-runbook-types.md#graphical-runbooks) Per usare questi runbook, vedere [Creazione grafica in Automazione di Azure.To](automation-graphical-authoring-intro.md)work with these runbooks, see Graphical authoring in Azure Automation .

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="editing-a-runbook-with-the-azure-portal"></a>Modifica di un runbook con il portale di AzureEditing a runbook with the Azure portal

Usare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nel portale di Azure selezionare l'account di automazione.
2. In **PROCESS AUTOMATION**selezionare **Runbooks** per aprire l'elenco dei runbook.
3. Scegliere il runbook da modificare e quindi fare clic su **Modifica**.
4. Modificare il runbook.
5. Al termine delle modifiche, fare clic su **Salva**.
6. Fare clic su **Pubblica** se si desidera pubblicare la versione bozza più recente del runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Inserire un cmdlet in un runbook

1. Nell'area di disegno dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il cmdlet.
2. Espandere il nodo **Cmdlet** nel controllo della libreria.
3. Espandere il modulo contenente il cmdlet da utilizzare.
4. Fare clic con il pulsante destro del mouse sul nome del cmdlet da inserire e scegliere **Aggiungi all'area di disegno**. Se il cmdlet dispone di più di un set di parametri, l'editor aggiunge il set predefinito. È inoltre possibile espandere il cmdlet per selezionare un set di parametri diverso.
5. Si noti che il codice per il cmdlet viene inserito con l'intero elenco di parametri.
6. Fornire un valore appropriato al posto del valore racchiuso tra parentesi angolari (<>) per tutti i parametri obbligatori. Rimuovere tutti i parametri non necessari.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Inserire codice per un runbook figlio in un runbook

1. Nell'area di disegno dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il [runbook figlio.](automation-child-runbooks.md)
2. Espandere il nodo **Runbook** nel controllo della libreria.
3. Fare clic con il pulsante destro sul runbook da inserire e selezionare **Aggiungi a canvas**.
4. Il codice relativo al runbook figlio viene inserito con qualsiasi segnaposto necessario per i parametri del runbook.
5. Sostituire i segnaposto con i valori appropriati per ogni parametro.

### <a name="insert-an-asset-into-a-runbook"></a>Inserire una risorsa in un runbook

1. Nel controllo Canvas dell'editor testuale posizionare il cursore nel punto in cui si desidera inserire il codice per il runbook figlio.
2. Espandere il nodo **Asset** nel controllo della libreria.
3. Espandere il nodo per il tipo di risorsa desiderato.
4. Fare clic con il pulsante destro del mouse sul nome della risorsa da inserire e selezionare **Aggiungi all'area di disegno**. Per [le risorse variabili](automation-variables.md), selezionare Aggiungi **"Ottieni variabile" all'area di disegno** o Aggiungi **"Imposta variabile" all'area di disegno,** a seconda che si desideri ottenere o impostare la variabile.
5. Si noti che il codice per l'asset viene inserito nel runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Modifica di un runbook di Automazione di Azure tramite Windows PowerShellEditing an Azure Automation runbook using Windows PowerShell

Per modificare un runbook con Windows PowerShell, usare l'editor desiderato e salvare il runbook in un file **con estensione ps1.** È possibile utilizzare il cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) per recuperare il contenuto del runbook. È possibile utilizzare il cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) per sostituire il runbook della bozza esistente con quello modificato.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recuperare il contenuto di un runbook tramite Windows PowerShellRetrieve the contents of a runbook using Windows PowerShell

I comandi di esempio seguenti mostrano come recuperare lo script per un runbook e salvarlo in un file di script. In questo esempio viene recuperata la versione bozza. È anche possibile recuperare la versione pubblicata del runbook, anche se questa versione non può essere modificata.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Modificare il contenuto di un runbook tramite Windows PowerShell

I comandi di esempio seguenti mostrano come sostituire il contenuto di un runbook con il contenuto di un file di script. Questa è la stessa procedura di esempio descritta in [Per importare un runbook da un file di script con Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Articoli correlati

* [Gestire runbook in Automazione di Azure](manage-runbooks.md)
* [Apprendimento del flusso di lavoro di PowerShellLearning PowerShell workflow](automation-powershell-workflow.md)
* [Creazione grafica in Automazione di AzureGraphical authoring in Azure Automation](automation-graphical-authoring-intro.md)
* [Certificati](automation-certificates.md)
* [Connessioni](automation-connections.md)
* [Credenziali](automation-credentials.md)
* [Pianificazioni](automation-schedules.md)
* [Variabili](automation-variables.md)

