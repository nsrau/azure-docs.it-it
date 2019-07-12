---
title: Aggiungere strumenti di migrazione in Azure Migrate
description: Viene descritto come aggiungere gli strumenti di migrazione nell'hub di Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811869"
---
# <a name="add-migration-tools"></a>Aggiungere strumenti di migrazione

Questo articolo descrive come aggiungere strumenti di migrazione in [Azure Migrate](migrate-overview.md).

Azure Migrate offre un hub di strumenti per la valutazione e migrazione in Azure. Include gli strumenti originali, gli strumenti forniti da altri servizi di Azure, mentre le offerte di fornitori di software indipendenti di terze parti.

Se si desidera aggiungere uno strumento di migrazione e non ancora impostato un progetto Azure Migrate, seguire questo [articolo](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Selezione di uno strumento di ISV

Se si sceglie un' [dello strumento di ISV](migrate-services-overview.md#isv-integration) per la migrazione, è possibile avviare da ottenere una licenza o iscriversi per una versione di valutazione gratuita, in conformità con i criteri di ISV. In ogni strumento, è disponibile un'opzione per connettersi a Azure Migrate. Distribuire lo strumento e seguire le istruzioni dello strumento e la documentazione per connettere l'area di lavoro dello strumento con Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Selezionare uno scenario di migrazione

1. Nel progetto Azure Migrate, fare clic su **Panoramica**.
2. Selezionare lo scenario di migrazione da usare:

    - Per eseguire la migrazione di carichi di lavoro e le macchine in Azure, selezionare **fase di valutazione e la migrazione di server**.
    - Per eseguire la migrazione di macchine SQL in locale, selezionare **fase di valutazione e la migrazione dei database**.
    - Per eseguire la migrazione delle App web in locale, selezionare **fase di valutazione e la migrazione di App web**.
    - Per eseguire la migrazione di grandi quantità di dati in locale in Azure in modalità offline, selezionare **ordinare una Data Box**.

    ![Scenario di valutazione](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selezionare uno strumento di migrazione server

1. Fare clic su **valutare ed eseguire la migrazione di server**.
2. Nelle **Azure Migrate - server**, se non sono stati aggiunti gli strumenti di migrazione, sotto **strumenti di migrazione**, selezionare **fare clic qui per aggiungere uno strumento di migrazione**. Se già stato aggiunto strumenti di migrazione, in **aggiungere ulteriori strumenti di migrazione**, selezionare **modifica**.

    > [!NOTE]
    > Se è necessario passare a un altro progetto, in **Azure Migrate - server**, accanto a **vedere i dettagli per un progetto di migrazione diverso**, fare clic su **fare clic qui**.

3. Nelle **Azure Migrate**, selezionare lo strumento di migrazione da usare.
    - Se si usa la migrazione di Server eseguire la migrazione di Azure, è possibile configurare ed eseguire le migrazioni direttamente nel progetto Azure Migrate.
    - Se si usa uno strumento di valutazione di terze parti, passare al collegamento fornito per gli ISV ed eseguire la migrazione seguendo le istruzioni che offrono.

## <a name="select-a-database-migration-tool"></a>Selezionare uno strumento di migrazione di database

1. Fare clic su **valuta e la migrazione dei database**
2. Nelle **database**, fare clic su **aggiungere gli strumenti**.
3. In Aggiungi uno strumento > **lo strumento di migrazione selezionare**, selezionare lo strumento da usare per la migrazione del database.

## <a name="select-a-web-app-migration-tool"></a>Selezionare uno strumento di migrazione di app web

1. Fare clic su **fase di valutazione e la migrazione di App web**.
2. Seguire il collegamento allo strumento di migrazione per il servizio App di Azure. Usare lo strumento di migrazione per:

    - **Valutare le app online**: È possibile valutare e la migrazione di App con un URL pubblico online, usando Azure App Service Migration Assistant.
    - **.NET/PHP**: Per le app .NET e PHP interne, è possibile scaricare ed eseguire il Migration Assistant.

## <a name="order-an-azure-data-box"></a>Ordine di un Azure Data Box

Per eseguire la migrazione di grandi quantità di dati in Azure, è possibile ordinare un Azure DAta Box per il trasferimento dei dati offline.

1. Fare clic su **ordinare una Data Box**.
2. Nelle **selezionare l'Azure Data Box**, specificare la sottoscrizione. 
3. Il trasferimento sarà un'importazione in Azure. Specificare l'origine dati e la destinazione dell'area di Azure per i dati.

## <a name="next-steps"></a>Passaggi successivi

Provare una migrazione tramite la migrazione di Server eseguire la migrazione di Azure per [Hyper-V](tutorial-migrate-hyper-v.md) oppure [VMware](tutorial-migrate-vmware.md) macchine virtuali.
