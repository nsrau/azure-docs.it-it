---
title: Informazioni su come eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 8713447386e116b3f1ecb2754d46cc8d88d00b39
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Ci sono diversi modi per eseguire l'onboarding, ad esempio da una macchina virtuale, [dall'account di automazione](automation-onboard-solutions-from-automation-account.md) o da un [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni da una macchina virtuale di Azure.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com

## <a name="enable-the-solutions"></a>Abilitare le soluzioni

Passare a una macchina virtuale esistente e selezionare **Gestione aggiornamenti**, **Inventario** o **Rilevamento modifiche** in **OPERAZIONI**.

Scegliere l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Passare alle altre soluzioni e fare clic su **Abilita**. Le caselle di riepilogo a discesa relative a Log Analytics e all'account di Automazione sono disabilitate perché vengono usati la stessa area di lavoro e lo stesso account di automazione della soluzione abilitata in precedenza.

![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> Per **Rilevamento modifiche** e **Inventario** viene usata la stessa soluzione. Quando un servizio è abilitato, viene abilitato anche l'altro.

## <a name="scope-configuration"></a>Configurazione dell'ambito

Ogni soluzione usa una configurazione dell'ambito nell'area di lavoro per definire i computer di destinazione della soluzione. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della soluzione a computer specifici. Per accedere alle configurazioni dell'ambito, nell'account di Automazione in **RISORSE CORRELATE** selezionare **Area di lavoro**. In **Origini dati dell'area di lavoro** selezionare **Configurazioni ambito**.

Le due configurazioni dell'ambito create per impostazione predefinita sono **MicrosoftDefaultScopeConfig-ChangeTracking** e **MicrosoftDefaultScopeConfig-Updates**.

Fare clic sui puntini di sospensione (...) in una delle configurazioni e selezionare **Modifica**. Nella pagina **Modifica configurazione dell'ambito** selezionare **Selezionare i gruppi di computer** per aprire la pagina **Gruppi di computer**. Questa pagina mostra le ricerche salvate usate per creare la configurazione dell'ambito.

## <a name="saved-searches"></a>Ricerche salvate

Quando un computer viene aggiunto alle soluzioni Gestione aggiornamenti o Rilevamento modifiche e Inventario, viene aggiunto a una delle due ricerche salvate nell'area di lavoro. Queste ricerche salvate sono query che contengono i computer di destinazione per le soluzioni.

Passare all'area di lavoro e selezionare **Ricerche salvate** in **Generale**. Le due ricerche salvate usate da queste soluzioni sono indicate nella tabella seguente:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

Selezionare una ricerca salvata per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati.

![Ricerche salvate](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Passaggi successivi

Continuare con le esercitazioni sulle soluzioni per informazioni su come usarle.

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)
