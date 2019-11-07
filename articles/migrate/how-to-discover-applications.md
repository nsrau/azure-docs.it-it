---
title: Individuare app, ruoli e funzionalità installati nei server locali utilizzando Azure Migrate server Assessment
description: Viene descritto come individuare app, ruoli e funzionalità nei server locali utilizzando Azure Migrate server assessment.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 10/16/2019
ms.author: snehaa
ms.openlocfilehash: 40cb73848bab97ef9030cb0c4f3f17c984a1e0ec
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715779"
---
# <a name="discover-machine-apps-roles-and-features"></a>Individuare app, ruoli e funzionalità del computer 

> [!NOTE]
> Se questa funzionalità non è ancora visualizzata nel portale di Azure Migrate, bloccarsi. Verrà visualizzato nella settimana successiva.

Questo articolo descrive come individuare applicazioni, ruoli e funzionalità nei server locali, usando Azure Migrate: server assessment.

L'individuazione dell'inventario delle app e dei ruoli e delle funzionalità in esecuzione nei computer locali ti permette di identificare e pianificare un percorso di migrazione ad Azure personalizzato per i tuoi carichi di lavoro. 

Individuazione app con Azure Migrate: la valutazione del server è senza agente. Non è necessario installare nulla in computer e macchine virtuali. Server Assessment usa il dispositivo Azure Migrate per eseguire l'individuazione insieme alle credenziali Guest del computer. L'appliance accede in remoto ai computer usando le chiamate WMI e SSH. 

> [!NOTE]
> L'individuazione delle app è attualmente supportata solo per le macchine virtuali VMware ed è limitata all'individuazione. Non è ancora stata offerta una valutazione basata su app.  Attualmente la valutazione server attualmente valuta le VM VMware locali, le VM Hyper-V e i server fisici a livello di computer, per le migrazioni in modalità Lift-and-Shift.


## <a name="before-you-start"></a>Prima di iniziare

1. Esaminare le [limitazioni del supporto](migrate-support-matrix-vmware.md#application-discovery) per l'individuazione a livello di app.
2. Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
3. Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server assessment.
4. Verificare i [requisiti di VMware](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) per l'individuazione e la valutazione delle macchine virtuali VMware con l'appliance Azure migrate.
4. Verificare i [requisiti](migrate-support-matrix-vmware.md#assessment-appliance-requirements) per la distribuzione di Azure migrate Appliance.

## <a name="prepare-for-app-discovery"></a>Prepararsi per l'individuazione delle app

1. [Preparare la distribuzione dell'appliance](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware). La preparazione include la verifica delle impostazioni dell'appliance e la configurazione di un account che l'appliance userà per accedere a server vCenter.
2. Assicurarsi di disporre di un account utente (uno per server Windows e Linux) con autorizzazioni di amministratore per i computer in cui si desidera individuare app, ruoli e funzionalità.
3. [Distribuire l'appliance VMware](how-to-set-up-appliance-vmware.md) per avviare l'individuazione. Per distribuire l'appliance, scaricare e importare un modello OVA in VMware per creare l'appliance come macchina virtuale VMware. Configurare il dispositivo e quindi registrarlo con Azure Migrate.
2. Quando si distribuisce l'appliance, per avviare l'individuazione continua è necessario specificare quanto segue:
    - Nome del server vCenter a cui si desidera connettersi.
    - Credenziali create per l'appliance per la connessione server vCenter.
    - Le credenziali dell'account create per l'appliance per la connessione alle macchine virtuali Windows/Linux.

Dopo che l'appliance è stata distribuita e sono state fornite le credenziali, l'appliance avvia l'individuazione continua dei metadati della macchina virtuale e dei dati sulle prestazioni, nonché l'individuazione di app, funzionalità e ruoli.  La durata dell'individuazione delle app dipende dal numero di macchine virtuali disponibili. Richiede in genere un'ora per l'individuazione di app di 500 VM.

## <a name="review-and-export-the-inventory"></a>Esaminare ed esportare l'inventario

Al termine dell'individuazione, se sono state fornite le credenziali per l'individuazione delle app, è possibile esaminare ed esportare l'inventario delle app nel portale di Azure. 

1. In **Azure migrate-server** > **Azure migrate: server Assessment**, fare clic sul conteggio visualizzato per aprire la pagina **server individuati** .

    > [!NOTE]
    > In questa fase è anche possibile impostare facoltativamente il mapping delle dipendenze per i computer individuati, in modo che sia possibile visualizzare le dipendenze tra i computer che si desidera valutare. [Altre informazioni](how-to-create-group-machine-dependencies.md).

2. In **applicazioni individuate**fare clic sul conteggio visualizzato.
3. Nell' **inventario delle applicazioni**è possibile esaminare le app, i ruoli e le funzionalità individuate.
4. Per esportare l'inventario, in **server individuati**fare clic su **Esporta inventario delle app**.

L'inventario delle app viene esportato e scaricato in formato Excel. Il foglio di **inventario dell'applicazione** Visualizza tutte le app individuate in tutti i computer.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una valutazione](how-to-create-assessment.md) per la migrazione in modalità Lift-and-Shift dei server individuati.
- Valutare un SQL Server database usando [Azure migrate: valutazione del database](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
