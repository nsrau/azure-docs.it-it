---
title: "Esercitazione: Trasformare e proteggere l'API in Gestione API di Azure | Microsoft Docs"
description: Questa esercitazione descrive come proteggere l'API in Gestione API con i criteri di trasformazione e limitazione (limitazione della frequenza).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 04fcfa4712ec0b558140e942997060234b33f53e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91627766"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Esercitazione: Trasformare e proteggere l'API

L'esercitazione illustra come trasformare l'API affinché non riveli le informazioni sul back-end privato. Potrebbe, ad esempio, essere necessario nascondere le informazioni sullo stack di tecnologie in esecuzione nel back-end. Potrebbe anche essere necessario nascondere gli URL originali visualizzati nel corpo della risposta HTTP dell'API e reindirizzarli invece al gateway di Gestione API.

Questa esercitazione illustra quanto sia semplice aggiungere la protezione all'API back-end configurando il limite di frequenza con Gestione API di Azure. Ad esempio, è possibile limitare la frequenza delle chiamate API per evitarne l'uso eccessivo da parte degli sviluppatori. Per altre informazioni, vedere [Criteri di Gestione API](api-management-policies.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> -   Trasformare un'API per rimuovere le intestazioni di risposta
> -   Sostituire gli URL originali nel corpo della risposta dell'API con URL del gateway di Gestione API
> -   Proteggere un'API aggiungendo un criterio per il limite di frequenza (limitazione delle richieste)
> -   Testare le trasformazioni

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Criteri nel portale":::

## <a name="prerequisites"></a>Prerequisiti

-   Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
-   Comprendere il [concetto di criteri in Gestione API di Azure](api-management-howto-policies.md).
-   Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
-   Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Trasformare un'API per rimuovere le intestazioni di risposta

Questa sezione illustra come nascondere le intestazioni HTTP che non devono essere visualizzate dagli utenti. Questo esempio mostra come eliminare le intestazioni seguenti nella risposta HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testare la risposta originale

Per visualizzare la risposta originale:

1. Nell'istanza del servizio Gestione API, selezionare **API**.
1. Fare clic su **Demo Conference API** nell'elenco di API.
1. Selezionare la scheda **Test** nella parte superiore della schermata.
1. Selezionare l'operazione **GetSpeakers** e quindi **Invia**.

La risposta originale avrà un aspetto simile al seguente:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Criteri nel portale":::

Come si può notare, la risposta include le intestazioni **X-AspNet-Version** e **X-Powered by**.

### <a name="set-the-transformation-policy"></a>Impostare i criteri di trasformazione

1. Selezionare **Demo Conference API** > **Progettazione** > **Tutte le operazioni**.
4. Nella sezione **Elaborazione in uscita** selezionare l'icona dell'editor di codice ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Criteri nel portale" border="false":::

1. Posizionare il cursore all'interno dell'elemento **&lt;outbound&gt;** e selezionare **Show snippets** (Mostra frammenti) nell'angolo in alto a destra.
1. Nella finestra di destra, in **Transformation policies** (Criteri di trasformazione) fare clic su **+ Set HTTP header** (+ Imposta intestazione HTTP) due volte (per inserire due frammenti di criteri).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Criteri nel portale":::

1. Modificare il codice **\<outbound>** in modo che risulti simile al seguente:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Criteri nel portale"::: (Imposta intestazione HTTP)

1. Selezionare **Salva**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Sostituire gli URL originali nel corpo della risposta dell'API con URL del gateway di Gestione API

Questa sezione illustra come nascondere gli URL originali visualizzati nel corpo della risposta HTTP dell'API e reindirizzarli invece al gateway di Gestione API.

### <a name="test-the-original-response"></a>Testare la risposta originale

Per visualizzare la risposta originale:

1. Selezionare **Demo Conference API** > **Test**.
1. Selezionare l'operazione **GetSpeakers** e quindi **Invia**.

    Come si può notare, la risposta include gli URL originali del back-end:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Criteri nel portale":::


### <a name="set-the-transformation-policy"></a>Impostare i criteri di trasformazione

1.  Selezionare **Demo Conference API** > **Tutte le operazioni** > **Progettazione**.
1.  Nella sezione **Elaborazione in uscita** selezionare l'icona dell'editor di codice ( **</>** ).
1.  Posizionare il cursore all'interno dell'elemento **&lt;outbound&gt;** e selezionare **Show snippets** (Mostra frammenti) nell'angolo in alto a destra.
1.  Nella finestra a destra, in **Transformation policies** (Criteri di trasformazione) selezionare **Mask URLs in content** (Maschera URL nel contenuto). 
1.  Selezionare **Salva**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Proteggere un'API aggiungendo criteri relativi ai limiti di frequenza (limitazione delle richieste)

Questa sezione illustra come aggiungere la protezione per l'API back-end configurando i limiti di frequenza. Ad esempio, è possibile limitare la frequenza delle chiamate API per evitarne l'uso eccessivo da parte degli sviluppatori. In questo esempio il limite è impostato su 3 chiamate ogni 15 secondi per ogni ID sottoscrizione. Dopo 15 secondi, uno sviluppatore può ripetere la chiamata all'API.

1.  Selezionare **Demo Conference API** > **Tutte le operazioni** > **Progettazione**.
1.  Nella sezione **Elaborazione in ingresso** selezionare l'icona dell'editor di codice ( **</>** ).
1.  Posizionare il cursore all'interno dell'elemento **&lt;inbound&gt;** .

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Criteri nel portale" border="false":::

1.  Nella finestra a destra, in **Access restriction policies** (Criteri di restrizione dell'accesso) selezionare **+ Limit call rate per key** (+ Limita frequenza delle chiamate per chiave).
1.  Sostituire il codice **rate-limit-by-key** (nell'elemento **\<inbound\>** ) con il codice seguente:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testare le trasformazioni

A questo punto, se si esamina il codice nell'editor di codice, i criteri sono simili ai seguenti:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

La parte rimanente della sezione testa le trasformazioni dei criteri impostate in questo articolo.

### <a name="test-the-stripped-response-headers"></a>Testare le intestazioni della risposta eliminate

1. Selezionare **Demo Conference API** > **Test**.
1. Selezionare l'operazione **GetSpeakers** e quindi **Invia**.

    Come si può notare, le intestazioni sono state eliminate:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Criteri nel portale":::

### <a name="test-the-replaced-url"></a>Testare l'URL sostituito

1. Selezionare **Demo Conference API** > **Test**.
1. Selezionare l'operazione **GetSpeakers** e quindi **Invia**.

    Come si può notare, l'URL è stato sostituito.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Criteri nel portale":::

### <a name="test-the-rate-limit-throttling"></a>Testare il limite di frequenza (limitazione delle richieste)

1. Selezionare **Demo Conference API** > **Test**.
1. Selezionare l'operazione **GetSpeakers**. Fare clic su **Invia** tre volte di seguito.

    Dopo avere inviato la richiesta 3 volte, verrà visualizzata la risposta **429 Troppe richieste**.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Criteri nel portale":::

1. Attendere circa 15 secondi e fare si nuovo clic su **Invia**. Questa volta verrà visualizzata una risposta **200 OK**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
>
> -   Trasformare un'API per rimuovere le intestazioni di risposta
> -   Sostituire gli URL originali nel corpo della risposta dell'API con URL del gateway di Gestione API
> -   Proteggere un'API aggiungendo criteri relativi ai limiti di frequenza (limitazione delle richieste)
> -   Testare le trasformazioni

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Monitorare l'API](api-management-howto-use-azure-monitor.md)
