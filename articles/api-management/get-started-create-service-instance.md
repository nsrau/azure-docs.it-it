---
title: Creare un'istanza di Gestione API di Azure | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per creare una nuova istanza di Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Creare una nuova istanza del servizio Gestione API di Azure

In questa esercitazione vengono descritti i passaggi per la creazione di una nuova istanza di Gestione API con il [portale di Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti

+ Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Creare un nuovo servizio

1. Nel [portale di Azure](https://portal.azure.com/)selezionare **Nuova** > **Integrazione aziendale** > **Gestione API**.

    In alternativa scegliere **Nuova**, digitare `API management` nella casella di ricerca e premere INVIO. Fare clic su **Crea**.

2. Nella finestra **Servizio Gestione API** immettere un **nome** univoco per il servizio di Gestione API. Questo nome non può essere modificato in seguito.

    > [!TIP]
    > Nome del servizio viene usato per generare un nome di dominio predefinito nel formato *{nome} .azure-api.net.* Se si desidera usare un nome di dominio personalizzato, vedere [Configure a custom domain](configure-custom-domain.md) (Configurare un dominio personalizzato). <br/>
    > Il nome del servizio viene usato per fare riferimento al servizio e alla risorsa di Azure corrispondenti.

5. Selezionare una **sottoscrizione** tra le sottoscrizioni di Azure alle quali si ha accesso.
6. In **Gruppo di risorse**selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../azure-resource-manager/resource-group-overview.md#resource-groups) per altre informazioni.
7. In **Percorso**, selezionare l'area geografica in cui viene creato il servizio di Gestione API. Nell'elenco a discesa vengono visualizzate solo le aree dei servizi di Gestione API disponibili. 
9. Immettere un **Nome organizzazione**. Questo nome viene usato in varie posizioni. Ad esempio, il titolo del portale per sviluppatori e il mittente dei messaggi di poste elettronica di notifica.
10. In **Indirizzo di posta elettronica dell'amministratore** impostare l'indirizzo di posta elettronica a cui inviare tutte le notifiche da **Gestione API**.
11. In **Piano tariffario** impostare il livello **Sviluppatore** per valutare il servizio. Questo livello non è disponibile per la produzione. Per altre informazioni sulla scalabilità dei livelli di Gestione API, vedere [upgrade and scale](upgrade-and-scale.md) (Aggiornare e ridimensionare).
12. Scegliere **Create**.

    > [!TIP]
    > Di solito sono necessari da 20 a 30 minuti per creare un servizio di Gestione API. Se si seleziona **Aggiungi al dashboard** è più facile trovare un servizio appena creato.

## <a name="next-steps"></a>Passaggi successivi

[Publish an API with Azure API Management](#api-management-getstarted-publish-api.md) (Pubblicare API con Gestione API di Azure)