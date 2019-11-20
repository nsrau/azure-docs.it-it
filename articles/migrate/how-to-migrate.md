---
title: Aggiungere strumenti di migrazione in Azure Migrate
description: Informazioni su come aggiungere strumenti di migrazione in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185970"
---
# <a name="add-migration-tools"></a>Aggiungere strumenti di migrazione

Questo articolo descrive come aggiungere strumenti di migrazione in [Azure migrate](migrate-overview.md).

Azure Migrate offre un hub di strumenti per la valutazione e la migrazione ad Azure. Sono inclusi strumenti nativi, strumenti forniti da altri servizi di Azure e offerte di fornitori di software indipendenti (ISV) di terze parti.

Se si vuole aggiungere uno strumento di migrazione e non è stato ancora configurato un progetto di Azure Migrate, seguire questo [articolo](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Selezione di uno strumento ISV

Se si sceglie uno [strumento ISV](migrate-services-overview.md#isv-integration) per la migrazione, è possibile iniziare con una licenza o iscriversi per ottenere una versione di valutazione gratuita, in base ai criteri ISV. In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Distribuire lo strumento e seguire le istruzioni e la documentazione dello strumento per connettere l'area di lavoro dello strumento con Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Selezionare uno scenario di migrazione

1. Nel progetto di Azure Migrate fare clic su **Panoramica**.
2. Selezionare lo scenario di migrazione che si vuole usare:

    - Per eseguire la migrazione di computer e carichi di lavoro in Azure, selezionare **valuta ed Esegui la migrazione dei server**.
    - Per eseguire la migrazione di computer SQL locali, selezionare **valuta ed Esegui la migrazione dei database**.
    - Per eseguire la migrazione di app Web locali, selezionare **valuta ed Esegui la migrazione delle app Web**.
    - Per eseguire la migrazione di grandi quantità di dati locali ad Azure in modalità offline, selezionare **Order a data box**.

    ![Scenario di valutazione](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selezionare uno strumento di migrazione server

1. Fare clic su **valuta ed Esegui la migrazione dei server**.
2. In **Azure migrate server**, se non sono ancora stati aggiunti strumenti di migrazione, in **strumenti di migrazione**Selezionare **fare clic qui per aggiungere uno strumento di migrazione**. Se sono già stati aggiunti strumenti di migrazione, in **Aggiungi altri strumenti di migrazione**selezionare **Cambia**.

    > [!NOTE]
    > Se è necessario passare a un altro progetto, in **Azure migrate-Servers**, accanto a **Visualizza dettagli per un progetto di migrazione diverso**, fare clic **su fare clic qui**.

3. In **Azure migrate**selezionare lo strumento di migrazione che si vuole usare.
    - Se si utilizza Azure Migrate migrazione del server, è possibile configurare ed eseguire le migrazioni direttamente nel progetto Azure Migrate.
    - Se si utilizza uno strumento di valutazione di terze parti, passare al collegamento fornito per l'ISV ed eseguire la migrazione in base alle istruzioni fornite.

## <a name="select-a-database-migration-tool"></a>Selezionare uno strumento di migrazione del database

1. Fare clic su **valuta e migra database**
2. In **database**fare clic su **Aggiungi strumenti**.
3. In Aggiungi uno strumento > **selezionare lo**strumento di migrazione, selezionare lo strumento che si desidera utilizzare per eseguire la migrazione del database.

## <a name="select-a-web-app-migration-tool"></a>Selezionare uno strumento di migrazione di app Web

1. Fare clic su **valuta ed Esegui la migrazione delle app Web**.
2. Seguire il collegamento allo strumento di migrazione per il servizio app Azure. Usare lo strumento di migrazione per:

    - **Valutazione delle app online**: è possibile valutare ed eseguire la migrazione di app con un URL pubblico online, usando il servizio app Azure Migration Assistant.
    - **.Net/php**: per le app .NET e php interne, è possibile scaricare ed eseguire il Migration Assistant.

## <a name="order-an-azure-data-box"></a>Ordinare un Azure Data Box

Per eseguire la migrazione di grandi quantità di dati in Azure, è possibile ordinare un Azure DAta box per il trasferimento di dati offline.

1. Fare clic su **Ordina un data box**.
2. In **selezionare il Azure Data Box**specificare la sottoscrizione. 
3. Il trasferimento sarà un'importazione in Azure. Specificare l'origine dati e la destinazione dell'area di Azure per i dati.

## <a name="next-steps"></a>Passaggi successivi

Provare a eseguire una migrazione usando la migrazione di Azure Migrate server per le macchine virtuali [Hyper-V](tutorial-migrate-hyper-v.md) o [VMware](tutorial-migrate-vmware.md) .
