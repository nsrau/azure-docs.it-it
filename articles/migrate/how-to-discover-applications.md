---
title: Individua app, ruoli e funzionalità nei server locali con Azure Migrate
description: Informazioni su come individuare app, ruoli e funzionalità nei server locali con Azure Migrate Server Assessment.Learn how to discover apps, roles, and features on on-premises servers with Azure Migrate Server Assessment.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453583"
---
# <a name="discover-machine-apps-roles-and-features"></a>Scopri le app, i ruoli e le funzionalità del computer

Questo articolo descrive come individuare applicazioni, ruoli e funzionalità nei server locali usando Azure Migrate: Server Assessment.This article describes how to discover applications, roles, and features on-premises servers, using Azure Migrate: Server Assessment.

L'individuazione dell'inventario di app e ruoli/funzionalità in esecuzione nelle macchine locali consente di identificare e pianificare un percorso di migrazione ad Azure personalizzato per i carichi di lavoro.

> [!NOTE]
> L'individuazione delle app è attualmente disponibile solo per le macchine virtuali VMware ed è limitata solo all'individuazione. Non offriamo ancora una valutazione basata sulle app. Valutazione basata su computer per le macchine virtuali VMware locali, le macchine virtuali Hyper-V e i server fisici.

Individuazione delle app tramite Azure Migrate: la valutazione del server è senza agente. Non viene installato nulla nei computer e nelle macchine virtuali. La valutazione del server usa l'appliance di Azure Migrate per eseguire l'individuazione insieme alle credenziali guest del computer. L'appliance accede in remoto alle macchine VMware utilizzando le API VMware.


## <a name="before-you-start"></a>Prima di iniziare

1. Assicurarsi di aver creato un progetto di Azure Migrate.Make sure you've [created](how-to-add-tool-first-time.md) an Azure Migrate project.
2. Assicurarsi di aver [aggiunto](how-to-assess.md) lo strumento Azure Migrate: Server Assessment a un progetto.
4. Verificare i requisiti di [VMware](migrate-support-matrix-vmware.md#vmware-requirements) per l'individuazione e la valutazione delle macchine virtuali VMware con l'appliance Azure Migrate.
5. Verificare i requisiti per la distribuzione dell'appliance Azure Migrate.Check the [requirements](migrate-appliance.md) for deploying the Azure Migrate appliance.
6. [Verificare il supporto e i requisiti](migrate-support-matrix-vmware.md#application-discovery) per l'individuazione delle applicazioni.

## <a name="prepare-for-app-discovery"></a>Preparare l'individuazione delle app

1. [Preparare la distribuzione dell'appliance](tutorial-prepare-vmware.md). La preparazione include la verifica delle impostazioni dell'appliance e la configurazione di un account che verrà utilizzato dall'appliance per accedere a vCenter Server.
2. Assicurati di avere un account utente (uno per i server Windows e Linux) con autorizzazioni di amministratore per i computer in cui vuoi individuare app, ruoli e funzionalità.
3. [Distribuire l'appliance di Azure Migrate](how-to-set-up-appliance-vmware.md) per avviare l'individuazione. Per distribuire l'appliance, scaricare e importare un modello OVA in VMware per creare l'appliance come macchina virtuale VMware. Configurare l'appliance e quindi registrarla con Azure Migrate.You configure the appliance and then register it with Azure Migrate.
2. Quando si distribuisce l'appliance, per avviare l'individuazione continua è necessario specificare quanto segue:
    - Nome del server vCenter a cui si desidera connettersi.
    - Credenziali create per la connessione dell'appliance al server vCenter.
    - The account credentials you created for the appliance to connect to Windows/Linux VMs.

Dopo che l'appliance è stata distribuita e sono state fornite le credenziali, avvia l'individuazione continua dei metadati delle macchine virtuali e dei dati sulle prestazioni, insieme e l'individuazione di app, funzionalità e ruoli.  La durata dell'individuazione dell'app dipende dal numero di macchine virtuali disponibili. In genere richiede un'ora per l'individuazione delle app di 500 macchine virtuali.

## <a name="review-and-export-the-inventory"></a>Rivedere ed esportare l'inventario

Al termine dell'individuazione, se sono state fornite le credenziali per l'individuazione delle app, è possibile esaminare ed esportare l'inventario delle app nel portale di Azure.After discovery ends, if you provided credentials for app discovery, you can review and export the app inventory in the Azure portal.

1. In **Azure Migrate - Server** > **Azure Migrate: Server Valutazione**fare clic sul conteggio visualizzato per aprire la pagina Server **individuati.**

    > [!NOTE]
    > In questa fase è anche possibile impostare facoltativamente il mapping delle dipendenze per i computer individuati, in modo da poter visualizzare le dipendenze tra i computer che si desidera valutare. [Scopri di più](how-to-create-group-machine-dependencies.md).

2. In **Applicazioni individuate**fare clic sul conteggio visualizzato.
3. In **Inventario applicazioni**è possibile esaminare le app, i ruoli e le funzionalità individuati.
4. Per esportare l'inventario, in **Server individuati**fare clic su **Esporta inventario app**.

L'inventario delle app viene esportato e scaricato in formato Excel. La scheda **Inventario applicazioni** visualizza tutte le app individuate in tutte le macchine.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una valutazione](how-to-create-assessment.md) per la migrazione lift and shift dei server individuati.
- Valutare un database di SQL Server usando [Azure Migrate: Valutazione del database](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
