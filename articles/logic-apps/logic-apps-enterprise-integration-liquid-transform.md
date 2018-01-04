---
title: Convertire dati JSON con trasformazioni liquide - App Azure per la logica | Documenti Microsoft
description: Creare mappe per le trasformazioni di JSON avanzate mediante App per la logica e modello liquida o trasformazioni.
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Trasformazioni di JSON avanzate utilizzando il modello liquido
Le app di logica di Azure supporta basic JSON le trasformazioni tramite azioni operazione dati native come comporre o analizzare JSON. Logica App ora supporta anche trasformazioni JSON avanzate con modelli liquide. [Liquide](https://shopify.github.io/liquid/) è un linguaggio di modello open source per le app web flessibile.
 
In questo articolo, imparare a usare una mappa liquida o un modello, che può supportare trasformazioni JSON più complesse, ad esempio le iterazioni, flussi di controllo, variabili e così via. È necessario definire mapping JSON con questa mappa liquida JSON e archiviare tale mappa nell'account di integrazione, prima di poter eseguire una trasformazione liquida nell'app logica.

## <a name="prerequisites"></a>Prerequisiti
Di seguito sono indicati i prerequisiti per utilizzare l'azione liquida:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Conoscenza di base [come creare App per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

* Un basic [Account integrazione](logic-apps-enterprise-integration-create-integration-account.md).


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Creare e aggiungere il modello liquido o eseguire il mapping all'account di integrazione

1. Creare il modello di esempio liquide per questo esempio. Il modello liquido definisce la modalità di trasformazione input JSON come descritto di seguito:

   ```
   {%- assign deviceList = content.devices | Split: ', ' -%}
    {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
    }
    ```
    > [!NOTE]
    > Se si utilizza il modello liquido any [filtri](https://shopify.github.io/liquid/basics/introduction/#filters), tali filtri devono iniziare con lettere maiuscole. 

2. Accedere al [portale di Azure](https://portal.azure.com).

3. Nel menu principale di Azure, scegliere **tutte le risorse**. 

4. Nella casella di ricerca, specificare l'account di integrazione. Selezionare l'account.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Nel riquadro di account di integrazione, selezionare **mappe**.

   ![Selezione mappe](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Scegliere **Aggiungi** e fornire questi dettagli per la mappa:
  * **Nome**: il nome per la mappa, ovvero "JsontoJsonTemplate" in questo esempio.
  * **Tipo di mappa**: il tipo per la mappa. For JSON alla trasformazione JSON, è necessario selezionare **liquide**.
  * **Mappa**: un file di modello o una mappa liquide esistente da utilizzare per la trasformazione, ovvero "SimpleJsonToJsonTemplate.liquid" in questo esempio. È possibile utilizzare il selettore file per trovare il file.

    ![Aggiungere modello liquida](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>Aggiungere l'azione liquida per trasformare JSON nell'app logica

1. [Creare un'app per la logica](logic-apps-create-a-logic-app.md).

2. Aggiungere il [richiesta trigger](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) logica all'applicazione in uso.

3. Scegliere **+ nuovo passaggio > per aggiungere un'azione**. Cercare *liquide* nella casella di ricerca. Selezionare **liquido - trasformazione JSON in formato JSON**.

  ![Ricerca-azione-liquido](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Nel **contenuto** , quindi selezionare **corpo** da elenco di contenuto dinamico o un elenco di parametri, a seconda del valore visualizzato. 
  
  ![Selezionare corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Dal **mappa** elenco a discesa selezionare il modello liquido, ovvero JsonToJsonTemplate in questo esempio.

  ![Selezionare mappa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se l'elenco è vuoto, l'app logica probabilmente non è collegato all'account di integrazione. Per collegare l'account di integrazione con il modello liquida o mappa logica app, seguire questi passaggi:

   1. Nel menu app logica, selezionare **impostazioni flusso di lavoro**. 
   2. Dal **selezionare un account di integrazione** elenco, selezionare l'account di integrazione e scegliere **salvare**.

     ![Collegamento app per la logica per l'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>Testare l'app per la logica

   Invio dell'input JSON all'App logica da [Postman](https://www.getpostman.com/postman) o uno strumento simile. L'output JSON trasformato dall'app logica è simile al seguente:
  
   ![Output di esempio](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
* [Altre informazioni sulle mappe](../logic-apps/logic-apps-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")  

