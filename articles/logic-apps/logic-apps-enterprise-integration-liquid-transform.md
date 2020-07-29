---
title: Convertire i dati JSON con le trasformazioni liquide
description: Creare trasformazioni o mappe per le trasformazioni JSON avanzate usando App per la logica e un modello Liquid
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879174"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Eseguire trasformazioni JSON avanzate con i modelli Liquid in App per la logica di Azure

È possibile eseguire trasformazioni JSON di base nelle app per la logica tramite operazioni native sui dati, come **Componi** o **Analizza JSON**. Per eseguire trasformazioni JSON avanzate, è possibile creare modelli o mappe con [Liquid](https://shopify.github.io/liquid/), un linguaggio del modello open source per app Web flessibili. I modelli Liquid definiscono come trasformare l'output JSON e supportano trasformazioni JSON più complesse, ad esempio iterazioni, flussi di controllo, variabili e così via.

Prima di poter eseguire una trasformazione Liquid nell'app per la logica, è necessario innanzitutto definire il mapping da JSON a JSON con un modello Liquid e archiviare tale mappa nell'account di integrazione. In questo articolo viene illustrato come creare e usare il modello o la mappa Liquid.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) di base

* Informazioni di base sul [linguaggio del modello Liquid](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Creare un modello o una mappa Liquid per l'account di integrazione

1. Per questo esempio, creare il modello Liquid di esempio descritto in questo passaggio. Nel modello Liquid è possibile usare [filtri Liquid](https://shopify.github.io/liquid/basics/introduction/#filters), che usano le convenzioni di denominazione [DotLiquid](https://github.com/dotliquid/dotliquid) e C#.

   > [!NOTE]
   > Assicurarsi che i nomi di filtro usino la *maiuscola e minuscola* nel modello. In caso contrario, i filtri non funzioneranno. Inoltre, le mappe hanno [limiti di dimensioni dei file](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

   ```json
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

1. Nel [portale di Azure](https://portal.azure.com), nella casella di ricerca di Azure, immettere `integration accounts` e selezionare **account di integrazione**.

   ![Trova "account di integrazione"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Trovare e selezionare l'account di integrazione.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Nel riquadro **Panoramica** , in **componenti**, selezionare **Maps**.

    ![Selezionare il riquadro "mappe"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. Nel riquadro **mappe** selezionare **Aggiungi** e specificare i dettagli per la mappa:

   | Proprietà | valore | Descrizione | 
   |----------|-------|-------------|
   | **Nome** | `JsonToJsonTemplate` | Nome per la mappa, ovvero "JsonToJsonTemplate" in questo esempio | 
   | **Tipo di mapping** | **liquid** | Tipo di mappa. Per la trasformazione da JSON a JSON, è necessario selezionare **Liquid**. | 
   | **Mappa** | `SimpleJsonToJsonTemplate.liquid` | File di mappa o di modello Liquid esistente da usare per la trasformazione, ovvero "SimpleJsonToJsonTemplate.liquid" in questo esempio. Per trovare questo file, è possibile usare la selezione file. Per i limiti delle dimensioni della mappa, vedere [limiti e configurazione](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Aggiungi modello Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Aggiungere l'azione Liquid per la trasformazione JSON

1. Nel portale di Azure seguire questa procedura per [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella finestra di progettazione di app per la logica aggiungere il [trigger Request](../connectors/connectors-native-reqres.md#add-request) all'app per la logica.

1. Nel trigger scegliere **Nuovo passaggio**. Nella casella di ricerca immettere `liquid` come filtro e selezionare questa azione: **trasforma JSON in JSON-Liquid**

   ![Trovare e selezionare l'azione Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Aprire l'elenco **mappa** e selezionare il modello Liquid, ovvero "JsonToJsonTemplate" in questo esempio.

   ![Selezionare la mappa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se l'elenco di mappe è vuoto, l'app per la logica probabilmente non è collegata all'account di integrazione. 
   Per collegare l'app per la logica all'account di integrazione che include il modello o la mappa Liquid, seguire questa procedura:

   1. Nel menu dell'app per la logica selezionare **Impostazioni del flusso di lavoro**.

   1. Nell'elenco **selezionare un account di integrazione** selezionare l'account di integrazione e selezionare **Salva**.

      ![Collegare l'app per la logica all'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. A questo punto aggiungere la proprietà **Content** a questa azione. Aprire l'elenco **Aggiungi nuovo parametro** e selezionare **contenuto**.

   ![Aggiungere la proprietà "Content" all'azione](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Per impostare il valore della proprietà **contenuto** , fare clic all'interno della casella **contenuto** in modo che venga visualizzato l'elenco contenuto dinamico. Selezionare il token del **corpo** che rappresenta l'output del contenuto del corpo del trigger.

   ![Selezionare il token "Body" per il valore della proprietà "Content"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Al termine, l'azione si presenterà come in questo esempio:

   ![Azione "trasforma JSON in JSON" completata](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Testare l'app per la logica

Inviare l'input JSON all'app per la logica da [Postman](https://www.getpostman.com/postman) o da uno strumento analogo. L'output JSON trasformato dall'app per la logica è simile al seguente:
  
![Output di esempio](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Altri esempi di azioni Liquid
Liquid non è limitato alle sole trasformazioni JSON. Di seguito sono riportate altre azioni di trasformazione disponibili che usano Liquid.

* Trasforma JSON in testo
  
  Ecco il modello Liquid usato per questo esempio:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Ecco gli input e gli output di esempio:
  
   ![Output di esempio di trasformazione JSON in testo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Trasforma XML in JSON
  
  Ecco il modello Liquid usato per questo esempio:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Ecco gli input e gli output di esempio:

   ![Esempio di output di trasformazione XML in JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Trasforma XML in testo
  
  Ecco il modello Liquid usato per questo esempio:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Ecco gli input e gli output di esempio:

   ![Esempio di output di trasformazione XML in testo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni sulle Enterprise Integration Pack")  
* [Altre informazioni sulle mappe](../logic-apps/logic-apps-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")  

