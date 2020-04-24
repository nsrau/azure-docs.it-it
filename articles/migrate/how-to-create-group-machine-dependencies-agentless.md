---
title: Configurare l'analisi delle dipendenze senza agente in Azure Migrate server Assessment
description: Configurare l'analisi delle dipendenze senza agente in Azure Migrate server assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455707"
---
# <a name="set-up-agentless-dependency-visualization"></a>Configurare la visualizzazione delle dipendenze senza agenti 

Questo articolo descrive come configurare l'analisi delle dipendenze senza agente in Azure Migrate: server assessment. L' [analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare e comprendere le dipendenze tra i computer di cui si vuole valutare e migrare in Azure.


> [!IMPORTANT]
> La visualizzazione delle dipendenze senza agenti è attualmente disponibile in anteprima solo per le macchine virtuali VMware, individuate con lo strumento Azure Migrate: server assessment.
> Le funzionalità possono essere limitate o incomplete.
> Questa versione di anteprima è coperta dal supporto tecnico e può essere usata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Prima di iniziare

- Informazioni [sull'analisi delle](concepts-dependency-visualization.md#agentless-analysis) dipendenze senza agenti.
- [Esaminare](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) i prerequisiti e i requisiti di supporto per la configurazione della visualizzazione delle dipendenze senza agenti per le macchine virtuali VMware
- Assicurarsi di aver [creato](how-to-add-tool-first-time.md) un progetto Azure migrate.
- Se è già stato creato un progetto, verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server assessment.
- Assicurarsi di aver configurato un' [appliance Azure migrate](migrate-appliance.md) per individuare i computer locali. Informazioni su come configurare un'appliance per le macchine virtuali [VMware](how-to-set-up-appliance-vmware.md) . L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment.


## <a name="current-limitations"></a>Limitazioni correnti

- In questo momento non è possibile aggiungere o rimuovere un server da un gruppo, nella visualizzazione analisi delle dipendenze.
- Una mappa delle dipendenze per un gruppo di server non è attualmente disponibile.
- Attualmente i dati sulle dipendenze non possono essere scaricati in formato tabulare.

## <a name="create-a-user-account-for-discovery"></a>Creazione di un account utente per l'individuazione

Configurare un account utente in modo che la valutazione del server possa accedere alla macchina virtuale per l'individuazione. [Informazioni sui requisiti](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) dell'account.


## <a name="add-the-user-account-to-the-appliance"></a>Aggiungere l'account utente al dispositivo

Aggiungere l'account utente al dispositivo.

1. Aprire l'app gestione Appliance. 
2. Passare al pannello **specificare i dettagli di vCenter** .
3. In **individua applicazione e dipendenze dalle macchine virtuali**fare clic su **Aggiungi credenziali** .
3. Scegliere il **sistema operativo**, specificare un nome descrittivo per l'account e la**password** del **nome utente**/
6. Fare clic su **Save**.
7. Fare clic su **Salva e avvia individuazione**.

    ![Aggiungi account utente VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Avvia individuazione dipendenza

Scegliere i computer in cui si desidera abilitare l'individuazione delle dipendenze.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Aggiungi server**.
3. Nella pagina **Aggiungi server** scegliere l'appliance che sta scoprendo i computer pertinenti.
4. Dall'elenco computer selezionare le macchine virtuali.
5. Fare clic su **Aggiungi server**.

    ![Avvia individuazione dipendenza](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

È possibile visualizzare le dipendenze circa sei ore dopo l'avvio dell'individuazione delle dipendenze.

## <a name="visualize-dependencies"></a>Visualizza dipendenze

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Cercare il computer che si vuole visualizzare.
3. Nella colonna **dipendenze** fare clic su **Visualizza dipendenze** .
4. Modificare il periodo di tempo per cui si desidera visualizzare la mappa utilizzando l'elenco a discesa **durata tempo** .
5. Espandere il gruppo **client** per elencare i computer con una dipendenza nel computer selezionato.
6. Espandere il gruppo **porta** per elencare i computer che hanno una dipendenza dal computer selezionato.
7. Per passare alla visualizzazione mappa di uno dei computer dipendenti, fare clic sul nome del computer > **caricare la mappa del server**

    ![Espandere il gruppo di porte del server e caricare la mappa del server](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Espandi gruppo di client ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Espandere il computer selezionato per visualizzare i dettagli a livello di processo per ogni dipendenza.

    ![Espandere il server per visualizzare i processi](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Le informazioni sul processo per una dipendenza non sono sempre disponibili. Se non è disponibile, la dipendenza viene rappresentata con il processo contrassegnato come "processo sconosciuto".

## <a name="stop-dependency-discovery"></a>Arresta individuazione dipendenza

Scegliere i computer in cui si desidera arrestare l'individuazione delle dipendenze.

1. In **Azure migrate: server Assessment**fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Rimuovi server**.
3. Nella pagina **Rimuovi server** scegliere il **dispositivo** che sta individuando le macchine virtuali in cui si vuole arrestare l'individuazione delle dipendenze.
4. Dall'elenco computer selezionare le macchine virtuali.
5. Fare clic su **Rimuovi server**.


## <a name="next-steps"></a>Passaggi successivi

[Raggruppare i computer per la](how-to-create-a-group.md) valutazione.
