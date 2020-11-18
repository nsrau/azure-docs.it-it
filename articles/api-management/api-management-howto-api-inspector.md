---
title: 'Esercitazione: Eseguire il debug delle API in Gestione API di Azure con la traccia delle richieste'
description: Seguire le procedure di questa esercitazione per abilitare la traccia ed esaminare i passaggi di elaborazione delle richieste in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542315"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Esercitazione: Eseguire il debug delle API con la traccia delle richieste

Questa esercitazione descrive come esaminare (tracciare) l'elaborazione delle richieste in Gestione API di Azure per semplificare il debug e la risoluzione dei problemi dell'API. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Tracciare una chiamata di esempio
> * Rivedere i passaggi di elaborazione delle richieste

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="Controllo API":::

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

## <a name="verify-allow-tracing-setting"></a>Verificare l'impostazione Consenti traccia 

L'impostazione **Consenti traccia** per la sottoscrizione usata per l'API deve essere abilitata. Se si usa la sottoscrizione con accesso completo predefinita, è abilitata per impostazione predefinita. Per eseguire la verifica nel portale, passare all'istanza di Gestione API e selezionare **Sottoscrizioni**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Consentire la traccia per la sottoscrizione":::

## <a name="trace-a-call"></a>Tenere traccia di una chiamata

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Gestione API.
1. Selezionare **API**.
1. Fare clic su **Demo Conference API** nell'elenco di API.
1. Selezionare la scheda **Test**.
1. Selezionare l'operazione **GetSpeakers**.
1. Verificare che l'intestazione della richiesta HTTP includa **Ocp-Apim-Trace: True** e un valore valido per **Ocp-Apim-Subscription-Key**. In caso contrario, selezionare **+ Aggiungi intestazione** per aggiungere l'intestazione.
1. Fare clic su **Invia** per effettuare una chiamata API.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Configurare la traccia API":::

> [!TIP]
> Se il campo **Ocp-Apim-Subscription-Key** non viene popolato automaticamente nella richiesta HTTP, è possibile recuperarlo nel portale. Selezionare **Sottoscrizioni** e aprire il menu di scelta rapida ( **...** ) per la sottoscrizione. Selezionare **Mostra/Nascondi chiavi**. Se necessario, è possibile rigenerare le chiavi. Aggiungere quindi una chiave all'intestazione.

## <a name="review-trace-information"></a>Rivedere le informazioni di traccia

1. Al termine della chiamata, passare alla scheda **Tracia** nella **Risposta HTTP**.
1. Fare clic su uno dei collegamenti seguenti per passare alle informazioni di traccia dettagliate: **In ingresso**, **Back-end**, **In uscita**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Rivedere la traccia della risposta":::

    * **In ingresso**: mostra la richiesta originale che Gestione API ha ricevuto dal chiamante e i criteri applicati alla richiesta. Ad esempio, se sono stati aggiunti criteri in [Esercitazione: Trasformare e proteggere l'API](transform-api.md), questi verranno visualizzati qui.

    * **Back-end**: mostra le richieste che Gestione API ha inviato al back-end dell'API e la risposta che ha ricevuto.

    * **In uscita**: mostra tutti i criteri applicati alla risposta prima di restituirla al chiamante.

    > [!TIP]
    > Ogni passaggio mostra inoltre il tempo trascorso da quando la richiesta è stata ricevuta da Gestione API.

1. Nella scheda **Messaggio** l'intestazione **ocp-apim-trace-location** mostra la posizione dei dati di traccia archiviati nell'archivio BLOB di Azure. Se necessario, passare a questa posizione per recuperare la traccia.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Posizione della traccia in Archiviazione di Azure":::
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Tracciare una chiamata di esempio
> * Rivedere i passaggi di elaborazione delle richieste

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Usare le revisioni](api-management-get-started-revise-api.md)
