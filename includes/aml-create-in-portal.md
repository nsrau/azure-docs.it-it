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
ms.date: 07/21/2019
ms.openlocfilehash: 51bd3dfb33b1f445db8672e1b987ee6c6242e09c
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370935"
---
1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della propria sottoscrizione di Azure. 

1. Nell'angolo superiore sinistro del portale selezionare **Crea una risorsa**.

   ![Creare una risorsa nel portale di Azure](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Usare la barra di ricerca per trovare **Area di lavoro del servizio Machine Learning**.

1. Selezionare **Area di lavoro del servizio Machine Learning**.

1. Nel riquadro **Area di lavoro del servizio Machine Learning** selezionare **Crea** per iniziare.

1. Configurare la nuova area di lavoro specificandone il nome, la sottoscrizione, il gruppo di risorse e la località.

    ![Creare un'area di lavoro](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Campo|DESCRIZIONE
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.  
   Subscription |Selezionare la sottoscrizione di Azure da usare.
   Resource group | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse include risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. 
   Location | Selezionare la località più vicina agli utenti e alle risorse di dati per creare l'area di lavoro.

1. Dopo aver completato la configurazione dell'area di lavoro, selezionare **Crea**. 

   La creazione dell'area di lavoro può richiedere alcuni istanti.

   Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, presente anche nella sezione delle notifiche. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

   ![Stato di creazione dell'area di lavoro](./media/aml-create-in-portal/notifications.png)
