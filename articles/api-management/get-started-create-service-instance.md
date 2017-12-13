---
title: Creare un'istanza di Gestione API di Azure | Microsoft Docs
description: Seguire i passaggi di questa esercitazione per creare una nuova istanza di Gestione API di Azure.
services: api-management
documentationcenter: 
author: juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b7ea43419cbbb5f5d38f8ad721d564650c3681a6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Creare una nuova istanza del servizio Gestione API di Azure

Gestione API di Azure consente alle organizzazioni di pubblicare API per permettere a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei dati e dei servizi. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. Gestione API di Azure consente di creare e gestire gateway API moderni per servizi back-end esistenti ospitati ovunque. Per altre informazioni, vedere la [panoramica](api-management-key-concepts.md).

In questa guida introduttiva vengono descritti i passaggi per la creazione di una nuova istanza di Gestione API con il portale di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![Nuova istanza](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-new-service"></a>Creare un nuovo servizio

1. Nel [portale di Azure](https://portal.azure.com/)selezionare **Nuova** > **Integrazione aziendale** > **Gestione API**.

    In alternativa scegliere **Nuova**, digitare `API management` nella casella di ricerca e premere INVIO. Fare clic su **Crea**.

2. Nella finestra **Servizio Gestione API** immettere le impostazioni.

    ![Nuova istanza](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)

    | Impostazione      | Valore consigliato  | Descrizione              |
    | ------------ |  ------- | ---------------------------------|
    |**Nome**|Un nome univoco per il servizio Gestione API| Il nome non può essere modificato in seguito. Nome del servizio viene usato per generare un nome di dominio predefinito nel formato *{nome} .azure-api.net.* Se si desidera usare un nome di dominio personalizzato, vedere [Configure a custom domain](configure-custom-domain.md) (Configurare un dominio personalizzato). <br/> Il nome del servizio viene usato per fare riferimento al servizio e alla risorsa di Azure corrispondenti.|
    |**Sottoscrizione**|Sottoscrizione in uso | La sottoscrizione in cui verrà creata questa nuova istanza del servizio. È possibile selezionare una sottoscrizione tra le sottoscrizioni di Azure alle quali si ha accesso.|
    |**Gruppo di risorse**|*apimResourceGroup*|È possibile selezionare un gruppo di risorse nuovo o esistente. Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../azure-resource-manager/resource-group-overview.md#resource-groups) per altre informazioni.|
    |**Posizione**|*Stati Uniti occidentali*|Selezionare l'area geografica più vicina. Nell'elenco a discesa vengono visualizzate solo le aree dei servizi di Gestione API disponibili. |
    |**Nome organizzazione**|Il nome dell'organizzazione|Questo nome viene usato in numerose posizioni, ad esempio il titolo del portale per sviluppatori e il mittente delle notifiche tramite posta elettronica.|
    |**Indirizzo di posta elettronica dell'amministratore**|*admin@org.com*|Impostare l'indirizzo di posta elettronica a cui inviare tutte le notifiche da **Gestione API**.|
    |**Piano tariffario**|*Developer*|Impostare il livello **Sviluppatore** per valutare il servizio. Questo livello non è disponibile per la produzione. Per altre informazioni sulla scalabilità dei livelli di Gestione API, vedere [upgrade and scale](upgrade-and-scale.md) (Aggiornare e ridimensionare).|
3. Scegliere **Create**.

    > [!TIP]
    > Di solito sono necessari da 20 a 30 minuti per creare un servizio di Gestione API. Se si seleziona **Aggiungi al dashboard** è più facile trovare un servizio appena creato.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate seguendo questa procedura:


1. Nel portale di Azure selezionare ![freccia](./media/get-started-create-service-instance/arrow.png).
2. Selezionare **Gruppi di risorse**.
3. Cercare il gruppo di risorse.
4. Fare clic su ". . ." ed eliminare il gruppo.

![cleanup](./media/get-started-create-service-instance/cleanup.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare e pubblicare la prima API](import-and-publish.md)
