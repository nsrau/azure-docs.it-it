---
title: Usare i messaggi XML nei flussi di lavoro - App per la logica di Azure | Microsoft Docs
description: Elaborare, convalidare, trasformare e migliorare i messaggi XML nelle app per la logica e negli scenari aziendali usando Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 3fec4935f5317be4bf8c9e05f1c24a7c05381b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Convalidare e trasformare il linguaggio XML, codificare e decodificare file flat e migliorare le funzionalità di messaggi nelle app per la logica

Grazie all'uso delle app per la logica, è possibile elaborare i messaggi XML che si inviano e si ricevono. Questa funzionalità è inclusa in Enterprise Integration Pack. Per gli utenti con uno sfondo a BizTalk Server, Enterprise Integration Pack offre funzionalità simili per trasformare e convalidare i messaggi, usare i file flat e persino servirsi di XPath per migliorare o estrarre le proprietà specifiche di un messaggio. 

Per gli utenti che non hanno familiarità con questo spazio, queste funzionalità ampliano le modalità di elaborazione dei messaggi nel flusso di lavoro. Ad esempio, in uno scenario Business-to-Business in cui si usano schemi XML specifici, è possibile usare Enterprise Integration Pack per migliorare le modalità di elaborazione aziendale dei messaggi. 

Enterprise Integration Pack include: 

* [Convalida dei messaggi XML](logic-apps-enterprise-integration-xml-validation.md "Informazioni sulla convalida dei messaggi XML"): consente di convalidare un messaggio XML in entrata o in uscita rispetto a uno schema specifico.
* [Trasformazione dei messaggi XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Informazioni sulle mappe e le trasformazioni di messaggi XML"): consente di convertire o personalizzare un messaggio XML in base ai propri requisiti o a quelli di un partner.
* [Codifica e decodifica del file flat](logic-apps-enterprise-integration-flatfile.md "Informazioni sulla codifica o decodifica del file flat"): consente di codificare o decodificare un file flat. Ad esempio, SAP accetta e invia file IDOC in formato file flat. Molte piattaforme di integrazione creano messaggi XML, tra cui App per la logica. Pertanto, è possibile creare un'app per la logica che usi il codificatore di file flat per "convertire" i file XML in file flat. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx): consente di arricchire un messaggio ed estrarre le proprietà specifiche del messaggio. È possibile quindi usare le proprietà estratte per indirizzare il messaggio a un endpoint intermedio o di destinazione.

## <a name="try-it-out"></a>Provare il servizio
[Distribuire un'app per la logica completamente operativa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (campione di GitHub) usando le funzionalità XML di App per la logica di Azure.

## <a name="learn-more"></a>Altre informazioni
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")
