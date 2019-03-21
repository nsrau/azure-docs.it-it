---
title: File di inclusione
description: File di inclusione
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588671"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Creare un argomento usando il portale di Azure
1. Nella pagina **Spazio dei nomi del bus di servizio** scegliere **Argomenti** dal menu a sinistra.
2. Selezionare **+ Argomento** sulla barra degli strumenti. 
4. Immettere un **nome** per l'argomento. Lasciare invariati i valori predefiniti delle altre opzioni.
5. Selezionare **Create**.

    ![Creare un argomento](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Creare sottoscrizioni per argomento
1. Selezionare l'**argomento** creato nella sezione precedente. 
    
    ![Selezionare l'argomento](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Nella pagina **Argomento del bus di servizio** scegliere **Sottoscrizioni** dal menu a sinistra e quindi selezionare **+ Sottoscrizione** sulla barra degli strumenti. 
    
    ![Pulsante Aggiungi sottoscrizione](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Nel **Crea sottoscrizione** pagina, immettere **S1** per **name** per la sottoscrizione e quindi selezionare **Create**. 

    ![Pagina Crea sottoscrizione](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Ripetere il passaggio precedente due volte per creare sottoscrizioni denominate **S2** e **S3**.