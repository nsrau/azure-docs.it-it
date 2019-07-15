---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/21/2019
ms.openlocfilehash: 2ec45b67367198c14fc9d03cdb659a51aed8a504
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841585"
---
1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della propria sottoscrizione di Azure. 

   ![Portale di Azure](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. Nell'angolo superiore sinistro del portale selezionare **Crea una risorsa**.

   ![Creare una risorsa nel portale di Azure](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Usare la barra di ricerca per selezionare **Area di lavoro del servizio Machine Learning**.

   ![Cercare un'area di lavoro](./media/aml-create-in-portal/allservices-search.png)

1. Nel riquadro **Area di lavoro del servizio Machine Learning** selezionare **Crea** per iniziare.

    ![Pulsante Crea](./media/aml-create-in-portal/portal-create-button.png)

1. Nel riquadro **Area di lavoro del servizio ML** configurare l'area di lavoro.

    ![Creare un'area di lavoro](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Campo|DESCRIZIONE
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.  
   Sottoscrizione |Selezionare la sottoscrizione di Azure da usare.
   Gruppo di risorse | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. 
   Location | Selezionare la località più vicina agli utenti e alle risorse di dati. Si tratta della località in cui viene creata l'area di lavoro.

1. Rivedere la configurazione dell'area di lavoro e selezionare **Crea**. La creazione dell'area di lavoro può richiedere alcuni istanti.

1. Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, presente anche nella sezione delle notifiche. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

   ![Stato di creazione dell'area di lavoro](./media/aml-create-in-portal/notifications.png)
