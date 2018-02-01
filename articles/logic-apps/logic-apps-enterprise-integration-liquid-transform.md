---
title: Convertire i dati JSON con le trasformazioni Liquid - App per la logica di Azure | Microsoft Docs
description: Creare trasformazioni o mappe per le trasformazioni JSON avanzate usando App per la logica e un modello Liquid.
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
ms.openlocfilehash: c1a1a5530c19d39a8e37d122235c8340caa88570
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Eseguire trasformazioni JSON avanzate con un modello Liquid

App per la logica di Azure supporta trasformazioni JSON di base tramite operazioni native sui dati, come **Componi** o **Analizza JSON**. Per le trasformazioni JSON avanzate, è possibile usare modelli Liquid con le app per la logica. 
[Liquid](https://shopify.github.io/liquid/) è un linguaggio del modello open source per app Web flessibili.
 
Questo articolo illustra come usare una mappa o un modello Liquid, che supporta trasformazioni JSON più complesse, ad esempio iterazioni, flussi di controllo, variabili e così via. Prima di eseguire una trasformazione Liquid nell'app per la logica, è necessario definire il mapping da JSON a JSON con una mappa Liquid e archiviare tale mappa nell'account di integrazione.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, è possibile [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md) Basic


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Creare un modello o una mappa Liquid per l'account di integrazione

1. Creare il modello Liquid per questo esempio. Il modello Liquid definisce come trasformare l'input JSON, come illustrato di seguito:

   ``` json
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
   > Se il modello Liquid usa [filtri](https://shopify.github.io/liquid/basics/introduction/#filters), i nomi dei filtri devono iniziare con una lettera maiuscola. 

2. Accedere al [portale di Azure](https://portal.azure.com).

3. Dal menu principale di Azure scegliere **Tutte le risorse**. 

4. Nella casella di ricerca trovare e selezionare l'account di integrazione.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Nel riquadro dell'account di integrazione selezionare **Mappe**.

   ![Selezionare Mappe](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Scegliere **Aggiungi** e specificare questi dettagli per la mappa:

   * **Nome**: nome per la mappa, ovvero "JsontoJsonTemplate" in questo esempio.
   * **Tipo di mapping**: tipo di mappa. Per la trasformazione da JSON a JSON, è necessario selezionare **Liquid**.
   * **Mappa**: file di mappa o modello Liquid esistente da usare per la trasformazione, ovvero "SimpleJsonToJsonTemplate.liquid" in questo esempio. Per trovare questo file, è possibile usare la selezione file.

   ![Aggiungere un modello Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Aggiungere l'azione Liquid per la trasformazione JSON

1. [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Aggiungere il [trigger di richiesta](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) all'app per la logica.

3. Scegliere **+ Nuovo passaggio > Aggiungi un'azione**. Nella casella di ricerca immettere *liquid* e selezionare **Liquid - Trasforma JSON in JSON**.

   ![Trovare e selezionare l'azione Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Nella casella **Contenuto** selezionare **Corpo** nell'elenco di contenuto dinamico o nell'elenco di parametri, a seconda di quale viene visualizzato.
  
   ![Selezionare Corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Nell'elenco **Mappa** selezionare il modello Liquid, ovvero "JsonToJsonTemplate" in questo esempio.

   ![Selezionare la mappa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se l'elenco è vuoto, l'app per la logica probabilmente non è collegata all'account di integrazione. 
   Per collegare l'app per la logica all'account di integrazione che include il modello o la mappa Liquid, seguire questa procedura:

   1. Nel menu dell'app per la logica selezionare **Impostazioni del flusso di lavoro**.
   2. Nell'elenco **Selezionare un account di integrazione** selezionare l'account di integrazione e scegliere **Salva**.

   ![Collegare l'app per la logica all'account di integrazione](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testare l'app per la logica

Inviare l'input JSON all'app per la logica da [Postman](https://www.getpostman.com/postman) o da uno strumento analogo. L'output JSON trasformato dall'app per la logica è simile al seguente:
  
![Output di esempio](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
* [Altre informazioni sulle mappe](../logic-apps/logic-apps-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")  

