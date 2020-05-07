---
title: Individuare app, ruoli e funzionalità nei server locali con Azure Migrate
description: Informazioni su come individuare app, ruoli e funzionalità nei server locali con Azure Migrate Assessment server.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744623"
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



## <a name="deploy-the-azure-migrate-appliance"></a>Distribuire il dispositivo Azure Migrate

1. [Esaminare](migrate-appliance.md#appliance---vmware) i requisiti per la distribuzione di Azure migrate Appliance.
2. Esaminare gli URL di Azure a cui l'appliance dovrà accedere nei cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls).
3. [Esaminare i dati](migrate-appliance.md#collected-data---vmware) che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-vmware.md#port-access) dei requisiti di accesso alle porte per l'appliance.
5. [Distribuire il dispositivo Azure migrate](how-to-set-up-appliance-vmware.md) per avviare l'individuazione. Per distribuire l'appliance, scaricare e importare un modello OVA in VMware per creare l'appliance come macchina virtuale VMware. Configurare il dispositivo e quindi registrarlo con Azure Migrate.
6. Quando si distribuisce l'appliance, per avviare l'individuazione continua è necessario specificare quanto segue:
    - Nome del server vCenter a cui si desidera connettersi.
    - Credenziali create per l'appliance per la connessione server vCenter.
    - Le credenziali dell'account create per l'appliance per la connessione alle macchine virtuali Windows/Linux.

Dopo che l'appliance è stata distribuita e sono state fornite le credenziali, l'appliance avvia l'individuazione continua dei metadati della macchina virtuale e dei dati sulle prestazioni, nonché l'individuazione di app, funzionalità e ruoli.  La durata dell'individuazione delle app dipende dal numero di macchine virtuali disponibili. Richiede in genere un'ora per l'individuazione di app di 500 VM.

## <a name="prepare-a-user-account"></a>Preparare un account utente

Creare un account da usare per l'individuazione e aggiungerlo all'appliance.

### <a name="create-a-user-account-for-discovery"></a>Creazione di un account utente per l'individuazione

Configurare un account utente in modo che la valutazione del server possa accedere alla macchina virtuale per l'individuazione. [Informazioni sui requisiti](migrate-support-matrix-vmware.md#application-discovery) dell'account.


### <a name="add-the-user-account-to-the-appliance"></a>Aggiungere l'account utente al dispositivo

Aggiungere l'account utente al dispositivo.

1. Aprire l'app gestione Appliance. 
2. Passare al pannello **specificare i dettagli di vCenter** .
3. In **individua applicazione e dipendenze dalle macchine virtuali**fare clic su **Aggiungi credenziali** .
3. Scegliere il **sistema operativo**, specificare un nome descrittivo per l'account e la**password** del **nome utente**/
6. Fare clic su **Salva**.
7. Fare clic su **Salva e avvia individuazione**.

    ![Aggiungi account utente VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>Esaminare ed esportare l'inventario

Al termine dell'individuazione, se sono state fornite le credenziali per l'individuazione delle app, è possibile esaminare ed esportare l'inventario delle app nel portale di Azure.

1. In **Azure migrate-Servers** > **Azure migrate: server Assessment**, fare clic sul conteggio visualizzato per aprire la pagina **server individuati** .

    > [!NOTE]
    > In questa fase è anche possibile impostare facoltativamente il mapping delle dipendenze per i computer individuati, in modo che sia possibile visualizzare le dipendenze tra i computer che si desidera valutare. [Altre informazioni](how-to-create-group-machine-dependencies.md).

2. In **applicazioni individuate**fare clic sul conteggio visualizzato.
3. Nell' **inventario delle applicazioni**è possibile esaminare le app, i ruoli e le funzionalità individuate.
4. Per esportare l'inventario, in **server individuati**fare clic su **Esporta inventario delle app**.

L'inventario delle app viene esportato e scaricato in formato Excel. Il foglio di **inventario dell'applicazione** Visualizza tutte le app individuate in tutti i computer.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una valutazione](how-to-create-assessment.md) per la migrazione in modalità Lift-and-Shift dei server individuati.
- Valutare un SQL Server database usando [Azure migrate: valutazione del database](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
