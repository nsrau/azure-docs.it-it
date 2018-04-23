---
title: Aggiungere un modello tabulare di esempio per il server Azure Analysis Services | Microsoft Docs
description: Informazioni su come aggiungere un modello di esempio in Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>Esercitazione: Aggiungere un modello di esempio

In questa esercitazione si aggiunge un modello Adventure Works di esempio al server. Il modello di esempio è una versione completa dell'esercitazione sulla modellazione dei dati Adventure Works Internet Sales (1200). Un modello di esempio è utile per il test della gestione del modello, la connessione a strumenti e applicazioni client e l'esecuzione di query sui dati del modello.

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


