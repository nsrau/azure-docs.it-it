---
title: Messaggi XML per l'integrazione aziendale B2B - App per la logica di Azure | Microsoft Docs
description: Elaborare, convalidare, trasformare e migliorare i messaggi XML per le soluzioni B2B in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: 5f804bf656b423d6dbe8f5c2ed73ba54d8361000
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124536"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Messaggi XML e file flat in App per la logica di Azure con Enterprise Integration Pack

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
