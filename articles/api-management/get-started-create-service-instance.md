---
title: Avvio rapido - Creare un'istanza di Gestione API di Azure
description: Creare una nuova istanza del servizio Gestione API di Azure usando il portale di Azure.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708207"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Avvio rapido: Creare una nuova istanza del servizio Gestione API di Azure usando il portale di Azure

Gestione API di Azure consente alle organizzazioni di pubblicare API per permettere a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei dati e dei servizi. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. Gestione API di Azure consente di creare e gestire gateway API moderni per servizi back-end esistenti ospitati ovunque. Per altre informazioni, vedere la [panoramica](api-management-key-concepts.md).

In questa guida introduttiva vengono descritti i passaggi per la creazione di una nuova istanza di Gestione API con il portale di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Istanza di Gestione API

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-new-service"></a>Creare un nuovo servizio

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. È anche possibile selezionare **Creare una risorsa** nella pagina **Home** di Azure. 
   
   Selezionare :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Crea una risorsa":::

   
1. Nella pagina **Nuovo** selezionare **Integrazione** > **Gestione API**.

   Nuova istanza di Gestione API
   
1. Nella pagina **Servizio Gestione API** immettere le impostazioni.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Crea una risorsa":::
   
   | Impostazione                 | DESCRIZIONE   |                                                                     
   |-------------------------|-----------------------------------------------|
   | nome - **                | Un nome univoco per il servizio Gestione API. Il nome non può essere modificato in seguito. Il nome del servizio fa riferimento sia al servizio che alla risorsa di Azure corrispondente. <br/> Il nome del servizio viene usato per generare un nome di dominio predefinito: *\<name\>.azure-api.net.* Se si desidera usare un nome di dominio personalizzato, vedere [Configure a custom domain](configure-custom-domain.md) (Configurare un dominio personalizzato). |
   | **--subscription**:          | La sottoscrizione in cui verrà creata questa nuova istanza del servizio.   |
   | **Gruppo di risorse**      |  Selezionare un gruppo di risorse nuovo o esistente. Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. |
   | **Posizione**          | Selezionare un'area geografica nelle vicinanze dalle posizioni del servizio Gestione API disponibili. | 
   | **Nome organizzazione**   | Il nome dell'organizzazione. Questo nome viene usato in numerose posizioni, ad esempio il titolo del portale per sviluppatori e il mittente delle notifiche tramite posta elettronica. |                                                         
   | **Indirizzo di posta elettronica dell'amministratore** | L'indirizzo di posta elettronica a cui inviare tutte le notifiche di **Gestione API**.   |  
   | **Piano tariffario**        | Selezionare il livello **Sviluppatore** per valutare il servizio. Questo livello non è disponibile per la produzione. Per altre informazioni sulla scalabilità dei livelli di Gestione API, vedere [upgrade and scale](upgrade-and-scale.md) (Aggiornare e ridimensionare). |

3. Selezionare **Crea**.

    > [!TIP]
    > Possono essere necessari da 30 a 40 minuti per creare e attivare un servizio Gestione API in questo livello. Se si seleziona **Aggiungi al dashboard** è più facile trovare un servizio appena creato.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Esaminare le proprietà del servizio nella pagina **Panoramica**.

   Istanza di Gestione API

Nel momento in cui l'istanza del servizio Gestione API è online, è possibile iniziare a usarla. Cominciare con l'esercitazione per [importare e pubblicare la prima API](import-and-publish.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate seguendo questa procedura:

1. Accedere al portale di Azure e selezionare **Gruppi di risorse**. È anche possibile selezionare **Gruppi di risorse** nella pagina **Home**. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Crea una risorsa":::

1. Nella pagina **Gruppi di risorse** selezionare il gruppo di risorse desiderato.

   Selezionare :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Crea una risorsa":::.

1. Nella pagina del gruppo di risorse selezionare **Elimina gruppo di risorse**. 
   
1. Digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.

   Eliminare un gruppo di risorse

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare e pubblicare la prima API](import-and-publish.md)
