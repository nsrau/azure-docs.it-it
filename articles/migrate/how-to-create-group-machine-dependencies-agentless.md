---
title: Configurare l'analisi delle dipendenze senza agente in Azure Migrate Server AssessmentSet up agentless dependency analysis in Azure Migrate Server Assessment
description: Configurare l'analisi delle dipendenze senza agente in Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455707"
---
# <a name="set-up-agentless-dependency-visualization"></a>Impostare la visualizzazione delle dipendenze senza agente 

Questo articolo descrive come configurare l'analisi delle dipendenze senza agente in Azure Migrate:Server Assessment.This article describes how to set up agentless dependency analysis in Azure Migrate:Server Assessment. [L'analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare e comprendere le dipendenze tra i computer che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify and understand dependencies across machines you want to assess and migrate to Azure.


> [!IMPORTANT]
> La visualizzazione delle dipendenze senza agente è attualmente in anteprima solo per le macchine virtuali VMware, rilevata con lo strumento Azure Migrate:Server Assessment.
> Le funzionalità potrebbero essere limitate o incomplete.
> Questa anteprima è coperta dal supporto clienti e può essere usata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni sull'analisi](concepts-dependency-visualization.md#agentless-analysis) delle dipendenze senza agenti.
- [Esaminare](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) i prerequisiti e i requisiti di supporto per l'impostazione della visualizzazione delle dipendenze senza agente per le macchine virtuali VMwareReview the prerequisites and support requirements for setting up agentless dependency visualization for VMware VMs
- Assicurarsi di aver creato un progetto di Azure Migrate.Make sure you've [created](how-to-add-tool-first-time.md) an Azure Migrate project.
- Se è già stato creato un progetto, assicurarsi di aver aggiunto lo strumento Azure Migrate:Server Assessment.If you've already created a project, make sure you've [added](how-to-assess.md) the Azure Migrate:Server Assessment tool.
- Assicurarsi di aver configurato [un'appliance Di Azure Migrate](migrate-appliance.md) per individuare le macchine locali. Informazioni su come configurare un'appliance per le macchine virtuali [VMware.](how-to-set-up-appliance-vmware.md) L'appliance individua i computer locali e invia metadati e dati sulle prestazioni a Azure Migrate:Server Assessment.The appliance discovers on-premises machines, and sends metadata and performance data to Azure Migrate:Server Assessment.


## <a name="current-limitations"></a>Limitazioni correnti

- Al momento non è possibile aggiungere o rimuovere un server da un gruppo, nella visualizzazione di analisi delle dipendenze.
- Una mappa delle dipendenze per un gruppo di server non è attualmente disponibile.
- Attualmente, i dati di dipendenza non possono essere scaricati in formato tabulare.

## <a name="create-a-user-account-for-discovery"></a>Creare un account utente per l'individuazioneCreate a user account for discovery

Configurare un account utente in modo che la valutazione del server possa accedere alla macchina virtuale per l'individuazione. [Ulteriori informazioni](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) sui requisiti dell'account.


## <a name="add-the-user-account-to-the-appliance"></a>Aggiungere l'account utente all'appliance

Aggiungere l'account utente all'appliance.

1. Aprire l'app di gestione dell'appliance. 
2. Passare al riquadro **dei dettagli Di vCenter.**
3. In **Individua applicazione e dipendenze nelle macchine virtuali**fare clic su Aggiungi **credenziali**
3. Scegliere il **sistema operativo**, specificare un nome descrittivo per l'account e la**password** del **nome**/utente
6. Fare clic su **Salva**.
7. Fare clic su **Salva e avviare l'individuazione**.

    ![Aggiungere l'account utente della macchina virtualeAdd VM user account](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Avviare l'individuazione delle dipendenzeStart dependency discovery

Scegliere i computer in cui si desidera abilitare l'individuazione delle dipendenze.

1. In **Azure Migrate: Server Assessment**fare clic su Server **individuati**.
2. Fare clic sull'icona **Analisi delle dipendenze.**
3. Fare clic su **Aggiungi server**.
3. Nella pagina **Aggiungi server** scegliere l'appliance che sta individuando i computer pertinenti.
4. Dall'elenco delle macchine, selezionare le macchine.
5. Fare clic su **Aggiungi server**.

    ![Avviare l'individuazione delle dipendenzeStart dependency discovery](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

È possibile visualizzare le dipendenze circa sei ore dopo l'avvio dell'individuazione delle dipendenze.

## <a name="visualize-dependencies"></a>Visualizzare le dipendenze

1. In **Azure Migrate: Server Assessment**fare clic su Server **individuati**.
2. Cercare la macchina che si desidera visualizzare.
3. Nella colonna **Dipendenze** fare clic su **Visualizza dipendenze**
4. Modificare il periodo di tempo per il quale si desidera visualizzare la mappa utilizzando l'elenco a discesa **Durata tempo.**
5. Espandere il gruppo **Client** per elencare i computer con una dipendenza dal computer selezionato.
6. Espandere il gruppo **Porta** per elencare i computer con una dipendenza dal computer selezionato.
7. Per passare alla vista mappa di una delle macchine dipendenti, fare clic sul nome del computer > **Carica mappa server**

    ![Espandere Gruppo di porte server e carica mappa server](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Espandi gruppo di client ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Espandere il computer selezionato per visualizzare i dettagli a livello di processo per ogni dipendenza.

    ![Espandere il server per visualizzare i processi](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Le informazioni sul processo per una dipendenza non sono sempre disponibili. Se non è disponibile, la dipendenza viene rappresentata con il processo contrassegnato come "Processo sconosciuto".

## <a name="stop-dependency-discovery"></a>Interrompere l'individuazione delle dipendenze

Scegliere i computer in cui si desidera interrompere l'individuazione delle dipendenze.

1. In **Azure Migrate: Server Assessment**fare clic su Server **individuati**.
2. Fare clic sull'icona **Analisi delle dipendenze.**
3. Fare clic su **Rimuovi server**.
3. Nella pagina **Rimuovi server** scegliere **l'appliance** che individua le macchine virtuali in cui si desidera arrestare l'individuazione delle dipendenze.
4. Dall'elenco delle macchine, selezionare le macchine.
5. Fare clic su **Rimuovi server**.


## <a name="next-steps"></a>Passaggi successivi

[Raggruppare le macchine](how-to-create-a-group.md) per la valutazione.
