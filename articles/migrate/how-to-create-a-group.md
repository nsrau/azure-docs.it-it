---
title: Raggruppare i computer per la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come raggruppare i computer prima di eseguire una valutazione con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 867124a08bbad88b7dac5386ee6bc1c9c4d99c12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549914"
---
# <a name="create-a-group-for-assessment"></a>Creare un gruppo per la valutazione

Questo articolo descrive come creare gruppi di computer per la valutazione con Azure Migrate: server assessment.

[Azure migrate](migrate-services-overview.md) consente di eseguire la migrazione ad Azure. Azure Migrate offre un hub centralizzato per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure dell'infrastruttura, delle applicazioni e dei dati locali. L'hub fornisce gli strumenti di Azure per la valutazione e la migrazione, oltre a offerte di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="grouping-machines"></a>Raggruppamento di computer

Si raccolgono i computer in gruppi per valutare se sono adatti per la migrazione ad Azure e per ottenere le stime di dimensionamento e costo di Azure. Esistono due modi per creare i gruppi:

- Se si conoscono i computer di cui è necessario eseguire la migrazione insieme, è possibile creare manualmente il gruppo in Azure Migrate.
- Se non si è certi dei computer che devono essere raggruppati, è possibile usare la funzionalità di visualizzazione delle dipendenze in Azure Migrate per creare gruppi. 

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="create-a-group-manually"></a>Creazione manuale di un gruppo

È possibile creare un gruppo nello stesso momento in cui viene [creata una valutazione](how-to-create-assessment.md).

Se si desidera creare un gruppo manualmente al di fuori della creazione di una valutazione, procedere come segue:

1. Nella **panoramica**> progetto Azure migrate fare clic su **valuta ed Esegui la migrazione dei server**. In **Azure migrate: server Assessment**, fare clic su **gruppi**
    - Se non è ancora stato aggiunto lo strumento Azure Migrate: server Assessment, fare clic per aggiungerlo. [Altre informazioni](how-to-assess.md)
    - Se non è ancora stato creato un progetto di Azure Migrate, vedere [altre informazioni](how-to-add-tool-first-time.md).

    ![Selezionare i gruppi](./media/how-to-create-a-group/select-groups.png)

2. Fare clic sull'icona di **gruppo** .
3. In **Crea gruppo**specificare un nome per il gruppo e in **nome Appliance**Selezionare il dispositivo Azure migrate in uso per l'individuazione del computer.
4. Dall'elenco computer, selezionare i computer che si desidera aggiungere al gruppo > **Crea**.

    ![Creare un gruppo](./media/how-to-create-a-group/create-group.png)

È ora possibile usare questo gruppo quando si [Crea una valutazione della macchina virtuale di Azure](how-to-create-assessment.md) o [una soluzione VMware di Azure (AVS)](how-to-create-azure-vmware-solution-assessment.md). Si noti che è possibile creare una valutazione AVS sui gruppi con solo macchine virtuali VMware. 

## <a name="refine-a-group-with-dependency-mapping"></a>Affinare un gruppo con mapping delle dipendenze

Il mapping delle dipendenze consente di visualizzare le dipendenze tra i computer. In genere si usa il mapping delle dipendenze quando si desidera valutare i gruppi di computer con livelli di confidenza più elevati.
- Consente di controllare le dipendenze tra computer, prima di eseguire una valutazione. 
- Consente inoltre di pianificare in modo efficace la migrazione ad Azure, assicurando che non venga lasciato nulla e quindi evitando interruzioni delle sorprese durante la migrazione.
- È possibile individuare i sistemi interdipendenti di cui è necessario eseguire la migrazione insieme e determinare se un sistema in esecuzione sta ancora servendo gli utenti o se è un candidato per la rimozione delle autorizzazioni anziché per la migrazione.

Se è già stato [configurato il mapping delle dipendenze](how-to-create-group-machine-dependencies.md)e si desidera affinare un gruppo esistente, procedere come segue:

1. Nella scheda **Server** , nel riquadro **Azure migrate: server Assessment** , fare clic su **gruppi**.
2. Fare clic sul gruppo che si desidera ridefinire.
    - Se non è ancora stato configurato il mapping delle dipendenze, nella colonna **dipendenze** viene visualizzato lo stato **richiesta installazione** . Per ogni macchina virtuale per cui si desidera visualizzare le dipendenze, fare clic su **richiede l'installazione**. Installare un paio di agenti in ogni macchina virtuale prima di poter eseguire il mapping delle dipendenze del computer. [Altre informazioni](how-to-create-group-machine-dependencies.md)

        ![Aggiungi mapping delle dipendenze](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Se è già stato configurato il mapping delle dipendenze, nella pagina gruppo fare clic su **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo.

3. Dopo aver fatto clic su **Visualizza dipendenze**, la mappa delle dipendenze del gruppo Mostra quanto segue:

    - Connessioni TCP in ingresso (client) e in uscita (Server) da e verso tutti i computer del gruppo in cui sono installati gli agenti di dipendenza.
    - I computer dipendenti in cui non sono installati gli agenti di dipendenza sono raggruppati in base ai numeri di porta.
    - I computer dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno del computer. Espandere ogni casella del computer per visualizzare i processi.
    - Proprietà del computer (incluso FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella del computer per visualizzare i dettagli.

4. Per visualizzare le dipendenze in un intervallo di tempo di propria scelta, modificare l'intervallo di tempo (un'ora per impostazione predefinita) specificando le date di inizio e di fine o la durata.

    > [!NOTE]
    > L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare [monitoraggio di Azure per eseguire query sui dati dipendenti](how-to-create-group-machine-dependencies.md) per un periodo di tempo più lungo.

5. Dopo aver identificato le dipendenze che si desidera aggiungere o rimuovere dal gruppo, è possibile modificare il gruppo. Usare Ctrl + clic per aggiungere o rimuovere computer dal gruppo.

    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione di computer invalida le valutazioni passate per un gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come configurare e usare il [mapping delle dipendenze](how-to-create-group-machine-dependencies.md) per creare gruppi con attendibilità elevata.

