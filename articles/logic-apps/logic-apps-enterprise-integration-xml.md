---
title: Messaggi XML e file flat
description: Elaborare, convalidare e trasformare i messaggi XML in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792143"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Messaggi XML e file flat in App per la logica di Azure con Enterprise Integration Pack

Nelle [app per la logica](logic-apps-overview.md)di Azure è possibile elaborare i messaggi XML inviati e ricevuti tramite Enterprise Integration Pack. Se è stato utilizzato BizTalk ServerBizTalk Server, Enterprise Integration Pack offre funzionalità simili per trasformare e convalidare i messaggi, utilizzare i file flat e persino utilizzare XPath per arricchire o estrarre proprietà specifiche da un messaggio. Se non si ha familiarità con questo spazio, queste funzionalità espandono la modalità di elaborazione dei messaggi nel flusso di lavoro dell'app per la logica. Ad esempio, se si dispone di uno scenario business-to-business (B2B) e si lavora con schemi XML specifici, è possibile utilizzare Enterprise Integration Pack per migliorare il modo in cui l'azienda elabora questi messaggi.

Ad esempio, Enterprise Integration Pack include le seguenti funzionalità:

* [Convalida XML:](logic-apps-enterprise-integration-xml-validation.md)convalida di un messaggio XML in ingresso o in uscita rispetto a uno schema specifico.

* [Trasformazione XML](logic-apps-enterprise-integration-transform.md): Convertire o personalizzare un messaggio XML in base alle proprie esigenze o ai requisiti di un partner utilizzando le mappe.

* [Codifica file flat e decodifica file flat](logic-apps-enterprise-integration-flatfile.md): Codificare o decodificare un file flat.

  Ad esempio, SAP accetta e invia file IDOC in formato file flat. Molte piattaforme di integrazione creano messaggi XML, tra cui App per la logica. Pertanto, è possibile creare un'app per la logica che usi il codificatore di file flat per "convertire" i file XML in file flat.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Arricchisce un messaggio ed estrae proprietà specifiche dal messaggio. È possibile quindi usare le proprietà estratte per indirizzare il messaggio a un endpoint intermedio o di destinazione.

## <a name="sample"></a>Esempio

[Distribuire un'app per la logica completamente operativa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (esempio GitHub) usando le funzionalità XML in App per la logica di Azure.Deploy a fully operational logic app (GitHub sample) by using the XML features in Azure Logic Apps.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
