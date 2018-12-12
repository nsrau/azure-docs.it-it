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
ms.openlocfilehash: 0b95441fd2805308c601509f1afc477f72bde321
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476284"
---
Accedere al [portale di Azure](https://portal.azure.com/) usando le credenziali della propria sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Il dashboard dell'area di lavoro del portale è supportato solo nei browser Microsoft Edge, Chrome e Firefox.

   ![Portale di Azure](./media/aml-create-in-portal/portal-dashboard.png)

Nell'angolo superiore sinistro del portale selezionare **Crea una risorsa**.

   ![Creare una risorsa nel portale di Azure](./media/aml-create-in-portal/portal-create-a-resource.png)

Nella barra di ricerca immettere **Machine Learning**. Selezionare il risultato di ricerca **Area di lavoro del servizio Machine Learning**.

   ![Cercare l'area di lavoro](./media/aml-create-in-portal/allservices-search.PNG)

Nel riquadro **Area di lavoro del servizio Machine Learning** scorrere fino in basso e selezionare **Crea** per iniziare.

   ![Create](./media/aml-create-in-portal/portal-create-button.png)

Nel riquadro **Area di lavoro del servizio ML** configurare l'area di lavoro.

   Campo|DESCRIZIONE
   ---|---
   Nome dell'area di lavoro |Immettere un nome univoco che identifichi l'area di lavoro. Qui viene usato il nome docs-ws. I nomi devono essere univoci all'interno del gruppo di risorse. Usare un nome facile da ricordare e da distinguere dai nomi delle aree di lavoro create da altri utenti.  
   Sottoscrizione |Selezionare la sottoscrizione di Azure da usare.
   Gruppo di risorse | Usare un gruppo di risorse esistente nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Qui viene usato il nome docs-aml. 
   Località | Selezionare la località più vicina agli utenti e alle risorse di dati. Si tratta della località in cui viene creata l'area di lavoro.

   ![Creare un'area di lavoro](./media/aml-create-in-portal/workspace-create.png)

Per avviare il processo di creazione selezionare **Crea**. La creazione dell'area di lavoro può richiedere alcuni istanti.

Per controllare lo stato della distribuzione, selezionare l'icona Notifiche (campanella) sulla barra degli strumenti.

   ![Stato di creazione dell'area di lavoro](./media/aml-create-in-portal/notifications.png)

Al termine del processo verrà visualizzato un messaggio di conferma del completamento della distribuzione, presente anche nella sezione delle notifiche. Per visualizzare la nuova area di lavoro, selezionare **Vai alla risorsa**.
