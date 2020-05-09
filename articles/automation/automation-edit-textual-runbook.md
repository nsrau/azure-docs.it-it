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
ms.openlocfilehash: aef6a58d18c0d9c1b18915786dd6e3359c31eda0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855786"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Modifica di runbook testuali in Automazione di Azure

L'editor di testo in Automazione di Azure può essere usato per modificare i [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) e i [runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Questo editor dispone delle caratteristiche tipiche di altri editor di codice, ad esempio IntelliSense. Dispone inoltre di codice a colori con funzionalità speciali aggiuntive per semplificare l'accesso alle risorse comuni a manuali operativi. 

L'editor di testo include una funzionalità per inserire codice per cmdlet, risorse e runbook figlio all'interno di un runbook. Invece di digitare il codice manualmente, è possibile selezionare da un elenco di risorse disponibili e l'editor inserisce il codice appropriato in Runbook.

Ogni runbook in Automazione di Azure include due versioni, ovvero una versione bozza e una versione pubblicata. Si modifica la versione bozza del runbook e quindi lo si pubblica in modo da poterlo eseguire. La versione pubblicata non può essere modificata. Per ulteriori informazioni, vedere la pagina relativa alla [pubblicazione di un Runbook](manage-runbooks.md#publish-a-runbook).

In questo articolo vengono fornite procedure dettagliate per l'esecuzione delle diverse funzioni disponibili in questo editor. Questi non sono applicabili ai [manuali operativi grafici](automation-runbook-types.md#graphical-runbooks). Per usare questi manuali operativi, vedere [creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Modifica di un runbook con il portale di Azure

Usare la procedura seguente per aprire un runbook per la modifica nell'editor di testo.

1. Nella portale di Azure selezionare l'account di automazione.
2. In **automazione processi**selezionare **manuali operativi** per aprire l'elenco di manuali operativi.
3. Scegliere il Runbook da modificare e quindi fare clic su **modifica**.
4. Modificare il runbook.
5. Al termine delle modifiche, fare clic su **Salva**.
6. Fare clic su **pubblica** se si vuole pubblicare la versione bozza più recente del Runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Inserire un cmdlet in un Runbook

1. Nel Canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il cmdlet.
2. Espandere il nodo **Cmdlet** nel controllo della libreria.
3. Espandere il modulo contenente il cmdlet da usare.
4. Fare clic con il pulsante destro del mouse sul nome del cmdlet da inserire e selezionare **Aggiungi a Canvas**. Se il cmdlet dispone di più di un set di parametri, l'editor aggiunge il set predefinito. È inoltre possibile espandere il cmdlet per selezionare un set di parametri diverso.
5. Si noti che il codice per il cmdlet viene inserito con l'intero elenco di parametri.
6. Fornire un valore appropriato al posto del valore racchiuso tra parentesi angolari (<>) per tutti i parametri obbligatori. Rimuovere tutti i parametri che non sono necessari.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Inserire il codice per un Runbook figlio in un Runbook

1. Nel Canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il [Runbook figlio](automation-child-runbooks.md).
2. Espandere il nodo **Runbook** nel controllo della libreria.
3. Fare clic con il pulsante destro sul runbook da inserire e selezionare **Aggiungi a canvas**.
4. Il codice relativo al runbook figlio viene inserito con qualsiasi segnaposto necessario per i parametri del runbook.
5. Sostituire i segnaposto con i valori appropriati per ogni parametro.

### <a name="insert-an-asset-into-a-runbook"></a>Inserire un asset in un Runbook

1. Nel controllo Canvas dell'editor di testo posizionare il cursore nel punto in cui si desidera inserire il codice per il Runbook figlio.
2. Espandere il nodo **Asset** nel controllo della libreria.
3. Espandere il nodo per il tipo di asset desiderato.
4. Fare clic con il pulsante destro del mouse sul nome dell'asset da inserire e selezionare **Aggiungi a Canvas**. Per [Asset variabili](automation-variables.md)selezionare **Aggiungi "Ottieni variabile" all'area di disegno** o **Aggiungi "Imposta variabile" su Canvas**, a seconda che si desideri ottenere o impostare la variabile.
5. Si noti che il codice per l'asset viene inserito nel runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Modifica di un Runbook di automazione di Azure con Windows PowerShell

Per modificare un runbook con Windows PowerShell, usare l'editor preferito e salvare il Runbook in un file con estensione **ps1** . È possibile usare il cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) per recuperare il contenuto di Runbook. È possibile usare il cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) per sostituire il Runbook bozza esistente con quello modificato.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recuperare il contenuto di un runbook con Windows PowerShell

I comandi di esempio seguenti mostrano come recuperare lo script per un runbook e salvarlo in un file di script. In questo esempio viene recuperata la versione bozza. È anche possibile recuperare la versione pubblicata di Runbook, anche se questa versione non può essere modificata.

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

## <a name="related-articles"></a>Articoli correlati

* [Gestire runbook in Automazione di Azure](manage-runbooks.md)
* [Apprendimento del flusso di lavoro PowerShell](automation-powershell-workflow.md)
* [Creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md)
* [Certificati](automation-certificates.md)
* [Connessioni](automation-connections.md)
* [Credenziali](automation-credentials.md)
* [Pianificazioni](automation-schedules.md)
* [variables](automation-variables.md)
* [Informazioni di riferimento sui cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
