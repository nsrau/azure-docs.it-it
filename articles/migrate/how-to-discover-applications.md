---
title: Individuare app, ruoli e funzionalità nei server locali con Azure Migrate
description: Informazioni su come individuare app, ruoli e funzionalità nei server locali con Azure Migrate Assessment server.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 6bdc26ac3a27026183e889bf258e2e1a5dadebfb
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315203"
---
# <a name="discover-machine-apps-roles-and-features"></a>Individuare app, ruoli e funzionalità del computer

Questo articolo descrive come individuare applicazioni, ruoli e funzionalità nei server locali, usando Azure Migrate: server assessment.

L'individuazione dell'inventario di app, ruoli e funzionalità in esecuzione in computer locali consente di identificare e adattare un percorso di migrazione ad Azure per i carichi di lavoro. App-Discovery usa l'appliance Azure Migrate per eseguire l'individuazione, usando le credenziali Guest della macchina virtuale. App-Discovery è senza agente. Nessun elemento viene installato nelle macchine virtuali.

> [!NOTE]
> L'individuazione delle app è attualmente disponibile in anteprima solo per le macchine virtuali VMware ed è limitata all'individuazione. Non è ancora stata offerta una valutazione basata su app. 


## <a name="before-you-start"></a>Prima di iniziare

- Assicurarsi di avere:
    - [Creazione](how-to-add-tool-first-time.md) di un progetto Azure migrate.
    - [Aggiunta](how-to-assess.md) dello strumento di valutazione Azure migrate: server a un progetto.
- Esaminare i [requisiti e il supporto per l'individuazione delle app](migrate-support-matrix-vmware.md#vmware-requirements).
- Verificare che le macchine virtuali in cui è in esecuzione App-Discovery dispongano di PowerShell versione 2,0 o successiva e che siano installati gli strumenti VMware (successivi a 10.2.0).
- Verificare i [requisiti](migrate-appliance.md) per la distribuzione di Azure migrate Appliance.


## <a name="deploy-the-azure-migrate-appliance"></a>Distribuire il dispositivo Azure Migrate

1. [Esaminare](migrate-appliance.md#appliance---vmware) i requisiti per la distribuzione di Azure migrate Appliance.
2. Esaminare gli URL di Azure a cui l'appliance dovrà accedere nei cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls).
3. [Esaminare i dati](migrate-appliance.md#collected-data---vmware) che l'appliance raccoglierà durante l'individuazione e la valutazione.
4. [Prendere nota](migrate-support-matrix-vmware.md#port-access-requirements) dei requisiti di accesso alle porte per l'appliance.
5. [Distribuire il dispositivo Azure migrate](how-to-set-up-appliance-vmware.md) per avviare l'individuazione. Per distribuire l'appliance, scaricare e importare un modello OVA in VMware per creare l'appliance come macchina virtuale VMware. Configurare il dispositivo e quindi registrarlo con Azure Migrate.
6. Quando si distribuisce l'appliance, per avviare l'individuazione continua è necessario specificare quanto segue:
    - Nome del server vCenter a cui si desidera connettersi.
    - Credenziali create per l'appliance per la connessione server vCenter.
    - Le credenziali dell'account create per l'appliance per la connessione alle macchine virtuali Windows/Linux.

Dopo che l'appliance è stata distribuita e sono state fornite le credenziali, l'appliance avvia l'individuazione continua dei metadati della macchina virtuale e dei dati sulle prestazioni, nonché l'individuazione di app, funzionalità e ruoli.  La durata dell'individuazione delle app dipende dal numero di macchine virtuali disponibili. Richiede in genere un'ora per l'individuazione di app di 500 VM.

## <a name="verify-permissions"></a>Verificare le autorizzazioni

È [stato creato un server vCenter account](./tutorial-discover-vmware.md#prepare-vmware) di sola lettura per l'individuazione e la valutazione. L'account di sola lettura necessita di privilegi abilitati per **Virtual Machines**  >  **le operazioni Guest**delle macchine virtuali, per interagire con la macchina virtuale per l'individuazione delle app.

### <a name="add-the-user-account-to-the-appliance"></a>Aggiungere l'account utente al dispositivo

Aggiungere l'account utente come segue:

1. Aprire l'app gestione Appliance. 
2. Passare al pannello **specificare i dettagli di vCenter** .
3. In **individua applicazione e dipendenze dalle macchine virtuali**fare clic su **Aggiungi credenziali** .
3. Scegliere il **sistema operativo**, specificare un nome descrittivo per l'account e la password del **nome utente** / **Password**
6. Fare clic su **Salva**.
7. Fare clic su **Salva e avvia individuazione**.

    ![Aggiungi account utente VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Esaminare ed esportare l'inventario

Al termine dell'individuazione, se sono state fornite le credenziali per l'individuazione delle app, è possibile esaminare ed esportare l'inventario delle app nel portale di Azure.

1. In **Azure migrate-Servers**  >  **Azure migrate: server Assessment**, fare clic sul conteggio visualizzato per aprire la pagina **server individuati** .

    > [!NOTE]
    > In questa fase è anche possibile impostare facoltativamente l'analisi delle dipendenze per i computer individuati, in modo che sia possibile visualizzare le dipendenze tra i computer che si desidera valutare. [Altre](concepts-dependency-visualization.md) informazioni sull'analisi delle dipendenze.

2. In **applicazioni individuate**fare clic sul conteggio visualizzato.
3. Nell' **inventario delle applicazioni**è possibile esaminare le app, i ruoli e le funzionalità individuate.
4. Per esportare l'inventario, in **server individuati**fare clic su **Esporta inventario delle app**.

L'inventario delle app viene esportato e scaricato in formato Excel. Il foglio di **inventario dell'applicazione** Visualizza tutte le app individuate in tutti i computer.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una valutazione](how-to-create-assessment.md) per i server individuati.
- Valutare SQL Server database usando [Azure migrate: valutazione del database](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).