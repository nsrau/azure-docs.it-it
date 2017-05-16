---
title: Convalidare la configurazione dell&quot;account di Automazione di Azure | Microsoft Docs
description: Questo articolo descrive come verificare che la configurazione dell&quot;account di Automazione sia impostata correttamente.
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Testare l'autenticazione con un account RunAs di Automazione di Azure
Al termine della creazione di un account di Automazione, è possibile eseguire un semplice test per verificare di poter completare l'autenticazione in Azure Resource Manager o nella distribuzione classica di Azure con l'account RunAs di Automazione appena creato o aggiornato.    

## <a name="automation-run-as-authentication"></a>Autenticazione con l'account RunAs di Automazione

1. Nel Portale di Azure aprire l'account di Automazione creato in precedenza.  
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook **AzureAutomationTutorialScript** e quindi fare clic su **Avvia** per avviarlo.  Verrà visualizzata la richiesta di confermare l'avvio del runbook.
4. Verrà creato un [processo del runbook](automation-runbook-execution.md) e verrà visualizzato il pannello Processo con lo stato del processo nel riquadro **Riepilogo processi** .  
5. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
6. Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.<br> ![Verifica del runbook dell'entità di sicurezza](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output** .
8. Nel pannello **Output** dovrebbe risultare che l'autenticazione è stata completata e che è stato restituito un elenco di tutte le risorse disponibili nel gruppo di risorse.
9. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.
10. Chiudere **Riepilogo processi** e il pannello del runbook **AzureAutomationTutorialScript** corrispondente.

## <a name="classic-run-as-authentication"></a>Autenticazione con l'account RunAs classico
Se le risorse verranno gestite nel modello di distribuzione classica, seguire questa procedura per verificare di poter completare l'autenticazione con il nuovo account RunAs classico.     

1. Nel Portale di Azure aprire l'account di Automazione creato in precedenza.  
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Selezionare il runbook **AzureClassicAutomationTutorialScript** e quindi fare clic su **Avvia** per avviarlo.  Verrà visualizzata la richiesta di confermare l'avvio del runbook.
4. Verrà creato un [processo del runbook](automation-runbook-execution.md) e verrà visualizzato il pannello Processo con lo stato del processo nel riquadro **Riepilogo processi** .  
5. Lo stato del processo è inizialmente *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passerà quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
6. Al termine del processo del runbook, lo stato visualizzato dovrebbe essere **Completato**.<br><br> ![Verifica del runbook dell'entità di sicurezza](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Per visualizzare i risultati dettagliati del runbook, fare clic sul riquadro **Output** .
8. Nel pannello **Output** dovrebbe risultare che l'autenticazione è stata completata e che è stato restituito un elenco di tutte le VM classiche nella sottoscrizione.
9. Chiudere il pannello **Output** per tornare al pannello **Riepilogo processi**.
10. Chiudere **Riepilogo processi** e il pannello del runbook **AzureClassicAutomationTutorialScript** corrispondente.

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

