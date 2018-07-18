---
title: Informazioni su come eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario per più VM in Automazione di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233938"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Abilitare le soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario in più VM

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding di computer. Ad esempio, è possibile eseguire l'onboarding della soluzione [da una macchina virtuale](automation-onboard-solutions-from-vm.md), dall'[account di Automazione](automation-onboard-solutions-from-automation-account.md), durante l'esplorazione delle macchine virtuali o tramite [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni durante l'esplorazione delle macchine virtuali in Azure.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com

## <a name="enable-solutions"></a>Abilitare soluzioni

Nel portale di Azure passare a **Macchine virtuali**.

Usando le caselle di controllo, selezionare le macchine virtuali che si vuole caricare con Rilevamento modifiche e Inventario o Gestione aggiornamenti. L'onboarding è disponibile per un massimo di tre diversi gruppi di risorse alla volta.

![Elenco di VM](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Usare i controlli dei filtri per modificare l'elenco di macchine virtuali e quindi fare clic sulla casella di controllo in alto per selezionare tutte le macchine virtuali nell'elenco.

Dalla barra dei comandi fare clic su **Servizi** e selezionare **Rilevamento modifiche**, **Inventario** o **Gestione aggiornamenti**.

> [!NOTE]
> Per **Rilevamento modifiche** e **Inventario** viene usata la stessa soluzione. Quando un servizio è abilitato, viene abilitato anche l'altro.

L'immagine seguente è relativa a Gestione aggiornamenti. Rilevamento modifiche e Inventario hanno lo stesso layout e lo stesso comportamento.

L'elenco di macchine virtuali viene filtrato per visualizzare solo le macchine virtuali che sono nella stessa sottoscrizione e località. Se le macchine virtuali sono in più di tre gruppi di risorse, vengono selezionati i primi tre gruppi di risorse.

Usare i controlli dei filtri per selezionare macchine virtuali da sottoscrizioni, località e gruppi di risorse diversi.

![Caricare la soluzione Gestione aggiornamenti](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Rivedere le scelte per l'area di lavoro di Log Analytics e l'account di Automazione. Una nuova area di lavoro e un nuovo account di Automazione sono selezionati per impostazione predefinita. Per usare un'area di lavoro di Log Analytics e un account di Automazione esistenti, fare clic su **modifica** per selezionarli dalla pagina **Configurazione**. Al termine, fare clic su **Salva**.

![Selezionare un'area di lavoro e un account](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Deselezionare la casella di controllo accanto alle macchine virtuali che non si vuole abilitare. Le macchine virtuali che non è possibile abilitare sono già deselezionate.

Fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

## <a name="troubleshooting"></a>risoluzione dei problemi

Durante l'onboarding di più computer, per alcuni computer potrebbe essere visualizzato **Cannot enable** (Abilitazione non consentita). Esistono diversi motivi per cui alcuni computer non possono essere abilitati. Le sezioni seguenti indicano i possibili motivi dello stato **Cannot enable** (Abilitazione non consentita) in una VM durante un tentativo di onboarding.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>La VM invia report a un'area di lavoro diversa: "\<workspaceName\>".  Modificare la configurazione per usarla per l'abilitazione

**Causa**: questo errore indica che la VM che si sta provando a caricare invia report a un'altra area di lavoro.

**Soluzione**: fare clic su **Use as configuration** (Usa come configurazione) per modificare l'account di Automazione e l'area di lavoro di Log Analytics di destinazione.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>La VM invia report a un'area di lavoro che non è disponibile in questa sottoscrizione

**Causa**: l'area di lavoro a cui la macchina virtuale invia i report:

* È in una sottoscrizione diversa
* Non esiste più
* È in un gruppo di risorse per cui non si hanno autorizzazioni di accesso

**Soluzione**: trovare l'account di Automazione associato all'area di lavoro a cui la VM invia i report e caricare la macchina virtuale modificando la configurazione dell'ambito.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>La versione o la distribuzione del sistema operativo della macchina virtuale non è supportata

**Causa:** la soluzione non è supportata per tutte le distribuzioni Linux o tutte le versioni di Windows.

**Soluzione:** vedere l'[elenco di client supportati](automation-update-management.md#clients) per la soluzione.

### <a name="classic-vms-cannot-be-enabled"></a>Le VM classiche non possono essere abilitate

**Causa**: le macchine virtuali che usano il modello di distribuzione classica non sono supportate.

**Soluzione**: eseguire la migrazione della macchina virtuale al modello di distribuzione Resource Manager. Per altre informazioni in proposito, vedere [Eseguire la migrazione di risorse del modello di distribuzione classica](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>La macchina virtuale viene arrestata (deallocata).

**Causa**: lo stato della macchina virtuale non è **In esecuzione**.

**Soluzione**: per caricare una VM in una soluzione la VM deve essere in esecuzione. Fare clic sul collegamento inline **Avvia macchina virtuale** per avviare la VM senza uscire dalla pagina.

## <a name="next-steps"></a>Passaggi successivi

Ora che la soluzione è abilitata per le macchine virtuali, vedere l'articolo Panoramica di Gestione aggiornamenti per informazioni su come visualizzare la valutazione degli aggiornamenti per i computer.

> [!div class="nextstepaction"]
> [Gestione aggiornamenti - Visualizzare la valutazione degli aggiornamenti](./automation-update-management.md#viewing-update-assessments)

Esercitazioni aggiuntive sulle soluzioni e su come usarle:

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)