---
title: Creare un modello tabulare di esempio per il server Azure Analysis Services | Microsoft Docs
description: Informazioni su come creare un modello di esempio in Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: 1cc36ebf4c410d4764eb28ab89d7f2a5e7749ae5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-create-a-sample-model"></a>Esercitazione: Creare un modello di esempio

In questa esercitazione si crea un modello Adventure Works di esempio. Il modello di esempio è una versione completa dell'esercitazione sulla modellazione dei dati Adventure Works Internet Sales (1200). Un modello di esempio è utile per il test della gestione del modello, la connessione a strumenti e applicazioni client e l'esecuzione di query sui dati del modello.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questa esercitazione, sono necessari:

- Un server di Azure Analysis Services
- Autorizzazioni di amministratore del server

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Creare un modello di esempio

1. Nell'area **Overview** (Panoramica) del server fare clic su **New model** (Nuovo modello).

    ![Creare un modello di esempio](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. In **New model** >  (Nuovo modello) **Choose a datasource** (Scegli origine dati) verificare che la voce **Sample data** (Dati di esempio) sia selezionata e quindi fare clic su **Add** (Aggiungi).

    ![Selezionare i dati di esempio](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. In **Overview** (Panoramica) verificare che l'esempio `adventureworks` sia stato creato.

    ![Selezionare i dati di esempio](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Il modello di esempio usa risorse di memoria cache. Se il modello di esempio non viene usato per il test, è necessario rimuoverlo dal server.

> [!NOTE]
> Questi passaggi descrivono come eliminare un modello da un server usando SQL Server Management Studio. È anche possibile eliminare un modello tramite la funzionalità della finestra di progettazione Web di anteprima.

1. In SSMS > **Esplora oggetti** fare clic su **Connetti** > **Analysis Services**.

2. In **Connetti al server** incollare il nome del server e quindi in **Autenticazione** scegliere **Active Directory - Universale con supporto MFA**, immettere il nome utente e infine fare clic su **Connetti**.

    ![Accesso](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul database di esempio `adventureworks` e quindi fare clic su **Elimina**.

    ![Eliminare il database di esempio](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Passaggi successivi 

[Connettersi in Power BI Desktop](analysis-services-connect-pbi.md)   
[Gestire ruoli e utenti del database](analysis-services-database-users.md)


