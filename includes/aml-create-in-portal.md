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
ms.date: 09/24/2018
ms.openlocfilehash: 05331c710817e575deb7729189c9b2d8ccbafd7d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54489573"
---
1. Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della propria sottoscrizione di Azure. 

   ![Portale di Azure](./media/aml-create-in-portal/portal-dashboard.png)

1. Nell'angolo superiore sinistro del portale selezionare **Crea una risorsa**.

   ![Creare una risorsa nel portale di Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

1. Nella barra di ricerca immettere **Machine Learning**. Selezionare il risultato di ricerca **Area di lavoro del servizio Machine Learning**.

   ![Cercare un'area di lavoro](./media/aml-create-in-portal/allservices-search.PNG)

1. Nel riquadro **Area di lavoro del servizio Machine Learning** scorrere fino alla parte inferiore della pagina e selezionare **Crea** per iniziare.

   ![Create](./media/aml-create-in-portal/portal-create-button.png)

1. Nel riquadro **Area di lavoro del servizio ML** configurare l'area di lavoro.

   Campo|DESCRIZIONE
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. In questo esempio si usa **docs-ws**. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.  
   Sottoscrizione |Selezionare la sottoscrizione di Azure da usare.
   Gruppo di risorse | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. In questo esempio si usa **docs-aml**. 
   Località | Selezionare la località più vicina agli utenti e alle risorse di dati. Si tratta della località in cui viene creata l'area di lavoro.

   ![Creare un'area di lavoro](./media/aml-create-in-portal/workspace-create.png)

1. Per avviare il processo di creazione selezionare **Crea**. La creazione dell'area di lavoro può richiedere alcuni istanti.

1. Per controllare lo stato della distribuzione, selezionare l'icona Notifiche, **campanella**, sulla barra degli strumenti.

1. Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, presente anche nella sezione delle notifiche. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.

   ![Stato di creazione dell'area di lavoro](./media/aml-create-in-portal/notifications.png)
