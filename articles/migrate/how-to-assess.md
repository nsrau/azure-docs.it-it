---
title: Aggiungere strumenti di valutazione in Azure Migrate | Microsoft Docs
description: Viene descritto come aggiungere strumenti di valutazione nell'hub Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 8d13222ec05fc97fa591ac67d636a1f8c5c88845
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715865"
---
# <a name="add-assessment-tools"></a>Aggiungere strumenti di valutazione

Questo articolo descrive come aggiungere strumenti di valutazione in [Azure migrate](migrate-overview.md).

Azure Migrate offre un hub di strumenti per la valutazione e la migrazione ad Azure. Sono inclusi strumenti di Azure Migrate, nonché altri strumenti e offerte di fornitori di software indipendenti (ISV).

Se si vuole aggiungere uno strumento di valutazione e non si dispone ancora di un progetto di Azure Migrate, seguire questo [articolo](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Selezionare uno strumento

Se si sceglie uno strumento non Azure Migrate per la valutazione, iniziare con una licenza o iscriversi per ottenere una versione di valutazione gratuita, in base ai criteri degli strumenti. Gli strumenti hanno un'opzione per connettersi a Azure Migrate. Seguire le istruzioni e la documentazione per connettere lo strumento a Azure Migrate. [Altre](migrate-services-overview.md) informazioni sugli strumenti di.


## <a name="select-an-assessment-scenario"></a>Selezionare uno scenario di valutazione

1. Nel progetto di Azure Migrate fare clic su **Panoramica**.
2. Selezionare lo scenario di valutazione che si vuole usare:

    - Per individuare e valutare i computer e i carichi di lavoro per la migrazione ad Azure, selezionare **valuta ed Esegui la migrazione dei server**.
    - Per valutare i computer SQL locali, selezionare **valuta ed Esegui la migrazione dei database**.
    - Per valutare le app Web locali, selezionare **valuta ed Esegui la migrazione delle app Web**.

    ![Scenario di valutazione](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selezionare uno strumento di valutazione del server 

1. Fare clic su **valuta ed Esegui la migrazione dei server**.
2. In **Azure migrate server**, se non è stato aggiunto uno strumento di valutazione, in **strumenti di valutazione**Selezionare **fare clic qui per aggiungere uno strumento di valutazione**. Se sono già stati aggiunti strumenti di valutazione, in **Aggiungi altri strumenti di valutazione**selezionare **Cambia**.

    > [!NOTE]
    > Se è necessario passare a un altro progetto, in **Azure migrate-Servers**, accanto a **Visualizza dettagli per un progetto di migrazione diverso**, fare clic **su fare clic qui**.

3. In **Azure migrate**selezionare lo strumento di valutazione che si desidera utilizzare.

    - Se si utilizza Azure Migrate server Assessment, è possibile configurare, eseguire e visualizzare le valutazioni direttamente nel progetto Azure Migrate.
    - Se si usa uno strumento di valutazione diverso, passare al collegamento fornito per il sito ed eseguire la valutazione in base alle istruzioni fornite.


## <a name="select-a-database-assessment-tool"></a>Selezionare uno strumento di valutazione del database

1. Fare clic su **valuta e migra database**
2. In **database**fare clic su **Aggiungi strumenti**.
3. In Aggiungi uno strumento > **Seleziona strumento di valutazione**selezionare lo strumento che si desidera utilizzare per valutare il database.

## <a name="select-a-web-app-assessment-tool"></a>Selezionare uno strumento di valutazione app Web

1. Fare clic su **valuta ed Esegui la migrazione delle app Web**.
2. Seguire il collegamento allo strumento di migrazione per il servizio app Azure. Usare lo strumento di migrazione per:

    - **Valutazione delle app online**: è possibile valutare le app con un URL pubblico online, usando il servizio app Azure Migration Assistant.
    - **.Net/php**: per le app .NET e php interne, è possibile scaricare ed eseguire il Migration Assistant.



## <a name="next-steps"></a>Passaggi successivi

Provare una valutazione usando Azure Migrate server assessment per macchine virtuali [VMware](tutorial-prepare-vmware.md) , [Hyper-V](tutorial-prepare-hyper-v.md)o [server fisici](tutorial-prepare-physical.md)
