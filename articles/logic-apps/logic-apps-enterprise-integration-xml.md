---
title: Messaggi XML e file flat-app per la logica di Azure
description: Elaborare, convalidare e trasformare i messaggi XML in app per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 730adf3e6ef3ddab5a8b0e927f94ffe3725358f4
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679914"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Messaggi XML e file flat in App per la logica di Azure con Enterprise Integration Pack

In [app](logic-apps-overview.md)per la logica di Azure è possibile elaborare i messaggi XML che vengono inviati e ricevuti usando il Enterprise Integration Pack. Se si è utilizzato BizTalk Server, il Enterprise Integration Pack fornisce funzionalità simili per trasformare e convalidare i messaggi, utilizzare file flat e persino utilizzare XPath per arricchire o estrarre proprietà specifiche da un messaggio. Se non si ha familiarità con questo spazio, queste funzionalità espandono la modalità di elaborazione dei messaggi nel flusso di lavoro dell'app per la logica. Se, ad esempio, si dispone di uno scenario business-to-business (B2B) e si utilizzano schemi XML specifici, è possibile utilizzare il Enterprise Integration Pack per migliorare la modalità di elaborazione dei messaggi da parte dell'azienda.

Ad esempio, il Enterprise Integration Pack include le seguenti funzionalità:

* [Convalida XML](logic-apps-enterprise-integration-xml-validation.md): convalidare un messaggio XML in entrata o in uscita rispetto a uno schema specifico.

* [Trasformazione XML](logic-apps-enterprise-integration-transform.md): convertire o personalizzare un messaggio XML in base ai requisiti o ai requisiti di un partner usando maps.

* [Codifica file flat e decodifica file flat](logic-apps-enterprise-integration-flatfile.md): codificare o decodificare un file flat.

  Ad esempio, SAP accetta e invia file IDOC in formato file flat. Molte piattaforme di integrazione creano messaggi XML, tra cui App per la logica. Pertanto, è possibile creare un'app per la logica che usi il codificatore di file flat per "convertire" i file XML in file flat.

* [XPath](workflow-definition-language-functions-reference.md#xpath): arricchire un messaggio ed estrarre proprietà specifiche dal messaggio. È possibile quindi usare le proprietà estratte per indirizzare il messaggio a un endpoint intermedio o di destinazione.

## <a name="sample"></a>Esempio

[Distribuire un'app per la logica completamente operativa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (esempio GitHub) usando le funzionalità XML di app per la logica di Azure.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
