---
title: Caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure | Microsoft Docs
description: Informazioni su come caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure.
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure

In questa esercitazione viene illustrato come caricare automaticamente le soluzioni per gli aggiornamenti, il rilevamento modifiche e l'inventario per le VM in Automazione di Azure:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure manualmente.
> * Installare e aggiornare i moduli di Azure necessari.
> * Importare un runbook che carica le macchine virtuali di Azure.
> * Avviare il runbook che carica le macchine virtuali di Azure automaticamente.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:
+ Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Account di Automazione](automation-offering-get-started.md) per gestire i computer.

## <a name="onboard-an-azure-virtual-machine-manually"></a>Caricare una macchina virtuale di Azure manualmente

1.  Aprire l'account di Automazione.
2.  Fare clic sulla pagina Inventario.
![Caricare la soluzione Inventario](media/automation-onboard-solutions/inventory-onboard.png)
3.  Selezionare un'area di lavoro Log Analytics esistente o creare una nuova. Fare clic sul pulsante Abilita.
4.  Al termine della notifica di onboarding della soluzione Rilevamento modifiche e inventario, fare clic sulla pagina Gestione aggiornamenti.
![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions/update-onboard.png)
4.  Fare clic sul pulsante Abilita che carica la soluzione per gli aggiornamenti.
5.  Al termine della notifica di onboarding della soluzione per gli aggiornamenti, fare clic sulla pagina Rilevamento modifiche.
![Caricare Rilevamento modifiche](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Fare clic sul pulsante Aggiungi macchina virtuale di Azure.
![Selezionare la VM per Rilevamento modifiche](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Selezionare una VM di Azure e fare clic sul pulsante Abilita per l'onboarding nella soluzione Rilevamento modifiche e inventario.
8.  Al termine della notifica di onboarding della VM, fare clic sulla pagina Gestione aggiornamenti.
![Caricare la VM per Gestione aggiornamenti](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Fare clic sul pulsante Aggiungi macchina virtuale di Azure.
![Selezionare la VM per Gestione aggiornamenti](media/automation-onboard-solutions/enable-update.png)
10.  Selezionare una VM di Azure e fare clic sul pulsante Abilita per l'onboarding nella soluzione Gestione aggiornamenti.

## <a name="install-and-update-required-azure-modules"></a>Installare e aggiornare i moduli di Azure necessari

Per automatizzare l'onboarding della soluzione, è necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare AzureRM.OperationalInsights.
1.  Fare clic sulla pagina Moduli.
![Aggiornare i moduli](media/automation-onboard-solutions/update-modules.png)
2.  Fare clic sul pulsante Aggiorna moduli di Azure per eseguire l'aggiornamento alla versione più recente. Attendere il completamento degli aggiornamenti.
3.  Fare clic sul pulsante Esplora raccolta per aprire la raccolta di moduli.
![Importare il modulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  Cercare AzureRM.OperationalInsights e importare questo modulo nell'account di Automazione.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Importare un runbook che carica le soluzioni nelle macchine virtuali di Azure

1.  Fare clic sulla pagina Runbook sotto la categoria "Automazione processi".
2.  Fare clic sul pulsante "Sfoglia raccolta".
3.  Cercare "aggiornamento e rilevamento modifiche" e importare il runbook nell'account di Automazione.
![Importare il runbook di onboarding](media/automation-onboard-solutions/import-from-gallery.png)
4.  Fare clic su "Modifica" per visualizzare l'origine del runbook e fare clic sul pulsante "Pubblica".
![Importare il runbook di onboarding](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Avviare il runbook che carica le macchine virtuali di Azure automaticamente

Per avviare questo runbook, è necessario avere caricato la soluzione per il rilevamento modifiche o gli aggiornamenti in una VM di Azure. Per i parametri sono necessari una macchina virtuale e un gruppo di risorse esistenti con la soluzione caricata.
1.  Aprire il runbook Enable-MultipleSolution.
![Runbook di soluzione multipla](media/automation-onboard-solutions/runbook-overview.png)
2.  Fare clic sul pulsante Avvia e immettere i valori seguenti per i parametri.
    *   VMNAME. Nome di una VM esistente da caricare nella soluzione per il rilevamento modifiche o gli aggiornamenti. Lasciare vuoto per caricare tutte le VM del gruppo di risorse.
    *   VMRESOURCEGROUP. Nome del gruppo di risorse di cui la VM è membro.
    *   SUBSCRIPTIONID. ID della sottoscrizione in cui si trova la nuova VM da caricare. Lasciare vuoto per usare la sottoscrizione dell'area di lavoro. Quando viene specificato un ID sottoscrizione diverso, l'account RunAs per questo account di Automazione deve essere aggiunto come collaboratore anche per questa sottoscrizione.
    *   ALREADYONBOARDEDVM. Nome della VM caricata manualmente nella soluzione per gli aggiornamenti o il rilevamento modifiche.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. Nome del gruppo di risorse di cui la VM è membro.
    *   SOLUTIONTYPE. Immettere "Updates" o "ChangeTracking"
    
    ![Parametri del runbook di soluzione multipla](media/automation-onboard-solutions/runbook-parameters.png)
3.  Fare clic su OK per avviare il processo del runbook.
4.  Monitorare lo stato ed eventuali errori nella pagina del processo del runbook.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Pianificazione di runbook](automation-schedules.md).