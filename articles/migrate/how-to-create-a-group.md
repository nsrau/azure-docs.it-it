---
title: Raggruppare i computer per la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come raggruppare i computer prima di eseguire una valutazione con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301686"
---
# <a name="create-a-group-for-assessment"></a>Creare un gruppo per la valutazione

Questo articolo descrive come creare gruppi di computer per la valutazione con Azure Migrate: Server Assessment.This article describes how to create groups of machines for assessment with Azure Migrate: Server Assessment.

[Azure Migrate](migrate-services-overview.md) consente di eseguire la migrazione ad Azure.Azure Migrate helps you to migrate to Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="grouping-machines"></a>Raggruppamento di macchine

È possibile raccogliere le macchine in gruppi per valutare se sono adatte per la migrazione ad Azure e per ottenere il ridimensionamento di Azure e le stime dei costi. Esistono due modi per creare gruppi:There are a couple of ways to create groups:

- Se si conoscono i computer di cui è necessario eseguire la migrazione insieme, è possibile creare manualmente il gruppo in Azure Migrate.If you know the machines that need be migrated together, you can manually create the group in Azure Migrate.
- Se non si è certi dei computer che devono essere raggruppati, è possibile usare la funzionalità di visualizzazione delle dipendenze in Azure Migrate per creare gruppi. 

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="create-a-group-manually"></a>Creare un gruppo manualmente

È possibile creare un gruppo contemporaneamente alla creazione di una [valutazione.](how-to-create-assessment.md)

Se si desidera creare un gruppo manualmente al di fuori della creazione di una valutazione, eseguire le operazioni seguenti:

1. Nel progetto Azure Migrate > **Panoramica**fare clic su **Valuta ed esegui la migrazione dei server**. In **Azure Migrate: Server Assessment**fare clic su **Gruppi**
    - Se non è ancora stato aggiunto lo strumento Azure Migrate: Server Assessment, fare clic per aggiungerlo. [Scopri di più](how-to-assess.md).
    - Se non è ancora stato creato un progetto Di Azure Migrate, [vedere Altro.](how-to-add-tool-first-time.md)

    ![Selezionare i gruppi](./media/how-to-create-a-group/select-groups.png)

2. Fare clic sull'icona **Gruppo.**
3. In **Crea gruppo**specificare un nome di gruppo e in Nome **accessorio**selezionare l'appliance Azure Migrate in uso per l'individuazione del computer.
1. Dall'elenco dei computer, selezionare i computer che si desidera aggiungere al gruppo > **Crea**.

    ![Creare un gruppo](./media/how-to-create-a-group/create-group.png)

È ora possibile utilizzare questo gruppo quando si crea una [valutazione.](how-to-create-assessment.md)

## <a name="refine-a-group-with-dependency-mapping"></a>Perfezionare un gruppo con mapping delle dipendenze

Il mapping delle dipendenze consente di visualizzare le dipendenze tra i computer. Il mapping delle dipendenze viene in genere utilizzato quando si desidera valutare i gruppi di computer con livelli di attendibilità più elevati.
- Consente di eseguire un controllo incrociato delle dipendenze dei computer prima di eseguire una valutazione. 
- Consente inoltre di pianificare in modo efficace la migrazione ad Azure, assicurandosi che non venga lasciato nulla e quindi evitando interruzioni a sorpresa durante la migrazione.
- È possibile individuare i sistemi interdipendenti che devono eseguire la migrazione insieme e identificare se un sistema in esecuzione è ancora al servizio degli utenti o è un candidato per la rimozione delle autorizzazioni anziché la migrazione.

Se è già stato impostato il mapping delle [dipendenze](how-to-create-group-machine-dependencies.md)e si desidera perfezionare un gruppo esistente, eseguire le operazioni seguenti:

1. Nella scheda **Server,** nel riquadro **Azure Migrate: Valutazione server,** fare clic su **Gruppi.**
2. Fare clic sul gruppo che si desidera perfezionare.
    - Se non è ancora stato impostato il mapping delle **dipendenze,** nella colonna Dipendenze verrà visualizzato lo stato **di installazione Richiede.** Per ogni macchina virtuale per cui si desidera visualizzare le dipendenze, fare clic su **Richiede installazione**. Installare un paio di agenti in ogni macchina virtuale, prima di poter eseguire il mapping delle dipendenze del computer. [Scopri di più](how-to-create-group-machine-dependencies.md).

        ![Aggiungere il mapping delle dipendenze](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se è già stato impostato il mapping delle dipendenze, nella pagina del gruppo fare clic su **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.

3. Dopo aver fatto clic su **Visualizza dipendenze**, la mappa delle dipendenze del gruppo mostra quanto segue:

    - Connessioni TCP in ingresso (client) e in uscita (server) da e verso tutti i computer del gruppo in cui sono installati gli agenti di dipendenza.
    - I computer dipendenti in cui non sono installati gli agenti di dipendenza sono raggruppati in base ai numeri di porta.
    - I computer dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno della macchina. Espandere ogni casella macchina per visualizzare i processi.
    - Proprietà del computer (inclusi FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella della macchina per visualizzare i dettagli.

4. Per visualizzare le dipendenze in un intervallo di tempo a scelta, modificare l'intervallo di tempo (un'ora per impostazione predefinita) specificando le date di inizio e di fine o la durata.

    > [!NOTE]
    > L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare [Monitoraggio di Azure per](how-to-create-group-machine-dependencies.md) eseguire query sui dati dipendenti per un periodo più lungo.

5. Dopo aver identificato le dipendenze che si desidera aggiungere o rimuovere dal gruppo, è possibile modificare il gruppo. Per aggiungere o rimuovere computer dal gruppo, premere CTRL .

    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione di computer invalida le valutazioni precedenti per un gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come impostare e usare il mapping delle [dipendenze](how-to-create-group-machine-dependencies.md) per creare gruppi con confidenza elevata.

