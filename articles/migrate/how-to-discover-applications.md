---
title: Individuare app, ruoli e funzionalità nei server locali con Azure Migrate
description: Informazioni su come individuare app, ruoli e funzionalità nei server locali con Azure Migrate Assessment server.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79453583"
---
# <a name="discover-machine-apps-roles-and-features"></a>Individuare app, ruoli e funzionalità del computer

Questo articolo descrive come individuare applicazioni, ruoli e funzionalità nei server locali, usando Azure Migrate: server assessment.

L'individuazione dell'inventario delle app e dei ruoli e delle funzionalità in esecuzione nei computer locali ti permette di identificare e pianificare un percorso di migrazione ad Azure personalizzato per i tuoi carichi di lavoro.

> [!NOTE]
> L'individuazione delle app è attualmente disponibile in anteprima solo per le macchine virtuali VMware ed è limitata all'individuazione. Non è ancora stata offerta una valutazione basata su app. Valutazione basata su computer per macchine virtuali VMware locali, VM Hyper-V e server fisici.

Individuazione app con Azure Migrate: la valutazione del server è senza agente. Nessun elemento viene installato nei computer e nelle macchine virtuali. Server Assessment usa il dispositivo Azure Migrate per eseguire l'individuazione insieme alle credenziali Guest del computer. L'appliance accede in remoto ai computer VMware usando le API VMware.


## <a name="before-you-start"></a>Prima di iniziare

1. Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
2. Assicurarsi di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server Assessment a un progetto.
4. Verificare i [requisiti di VMware](migrate-support-matrix-vmware.md#vmware-requirements) per l'individuazione e la valutazione delle macchine virtuali VMware con l'appliance Azure migrate.
5. Verificare i [requisiti](migrate-appliance.md) per la distribuzione di Azure migrate Appliance.
6. [Verificare il supporto e i requisiti](migrate-support-matrix-vmware.md#application-discovery) per l'individuazione delle applicazioni.

## <a name="prepare-for-app-discovery"></a>Prepararsi per l'individuazione delle app

1. [Preparare la distribuzione dell'appliance](tutorial-prepare-vmware.md). La preparazione include la verifica delle impostazioni dell'appliance e la configurazione di un account che l'appliance userà per accedere a server vCenter.
2. Assicurarsi di disporre di un account utente (uno per server Windows e Linux) con autorizzazioni di amministratore per i computer in cui si desidera individuare app, ruoli e funzionalità.
3. [Distribuire il dispositivo Azure migrate](how-to-set-up-appliance-vmware.md) per avviare l'individuazione. Per distribuire l'appliance, scaricare e importare un modello OVA in VMware per creare l'appliance come macchina virtuale VMware. Configurare il dispositivo e quindi registrarlo con Azure Migrate.
2. Quando si distribuisce l'appliance, per avviare l'individuazione continua è necessario specificare quanto segue:
    - Nome del server vCenter a cui si desidera connettersi.
    - Credenziali create per l'appliance per la connessione server vCenter.
    - Le credenziali dell'account create per l'appliance per la connessione alle macchine virtuali Windows/Linux.

Dopo che l'appliance è stata distribuita e sono state fornite le credenziali, l'appliance avvia l'individuazione continua dei metadati della macchina virtuale e dei dati sulle prestazioni, nonché l'individuazione di app, funzionalità e ruoli.  La durata dell'individuazione delle app dipende dal numero di macchine virtuali disponibili. Richiede in genere un'ora per l'individuazione di app di 500 VM.

## <a name="review-and-export-the-inventory"></a>Esaminare ed esportare l'inventario

Al termine dell'individuazione, se sono state fornite le credenziali per l'individuazione delle app, è possibile esaminare ed esportare l'inventario delle app nel portale di Azure.

1. In **Azure migrate-Servers** > **Azure migrate: server Assessment**, fare clic sul conteggio visualizzato per aprire la pagina **server individuati** .

    > [!NOTE]
    > In questa fase è anche possibile impostare facoltativamente il mapping delle dipendenze per i computer individuati, in modo che sia possibile visualizzare le dipendenze tra i computer che si desidera valutare. [Altre informazioni](how-to-create-group-machine-dependencies.md)

2. In **applicazioni individuate**fare clic sul conteggio visualizzato.
3. Nell' **inventario delle applicazioni**è possibile esaminare le app, i ruoli e le funzionalità individuate.
4. Per esportare l'inventario, in **server individuati**fare clic su **Esporta inventario delle app**.

L'inventario delle app viene esportato e scaricato in formato Excel. Il foglio di **inventario dell'applicazione** Visualizza tutte le app individuate in tutti i computer.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una valutazione](how-to-create-assessment.md) per la migrazione in modalità Lift-and-Shift dei server individuati.
- Valutare un SQL Server database usando [Azure migrate: valutazione del database](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
