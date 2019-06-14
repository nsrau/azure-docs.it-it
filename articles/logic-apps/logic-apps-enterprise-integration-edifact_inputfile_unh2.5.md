---
title: Gestire messaggi EDIFACT con i segmenti UNH 2.5 - App per la logica di Azure | Microsoft Docs
description: Risolvere documenti EDIFACT con segmenti UNH2.5 nelle App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681673"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Gestire documenti EDIFACT con segmenti UNH2.5 nelle App per la logica di Azure

Quando nel documento EDIFACT è presente un segmento UNH 2.5, viene usato per la ricerca dello schema. 

Esempio: Il campo UNH **EAN008** nel messaggio EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Procedura da seguire per gestire il messaggio 
1. Aggiornare lo schema
2. Controllare le impostazioni del contratto  

## <a name="update-the-schema"></a>Aggiornare lo schema
Per elaborare il messaggio, è necessario distribuire uno schema con il nome del nodo radice UNH 2.5.  Il nome radice dello schema ad esempio sarebbe **EFACT_D03B_ORDERS_EAN008**  

Per ogni D03B_ORDERS con un diverso segmento UNH2.5, è necessario distribuire uno schema individuale.  

## <a name="add-schema-to-the-edifact-agreement"></a>Aggiungere lo schema al contratto EDIFACT
### <a name="edifact-decode"></a>Decodifica EDIFACT
Per decodificare il messaggio in arrivo, configurare lo schema nelle impostazioni di ricezione del contratto EDIFACT
1. Aggiungere lo schema all'account di integrazione    
2. Configurare lo schema nelle impostazioni di ricezione del contratto EDIFACT. 
3. Selezionare il contratto EDIFACT e fare clic su **Modifica come JSON**.  Aggiungere il valore UNH2.5 nel contratto di ricezione **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Codifica EDIFACT
Per codificare il messaggio in arrivo, configurare lo schema nelle impostazioni di invio del contratto EDIFACT
1. Aggiungere lo schema all'account di integrazione    
2. Configurare lo schema nelle impostazioni di invio del contratto EDIFACT. 
3. Selezionare il contratto EDIFACT e fare clic su **Modifica come JSON**.  Aggiungere il valore UNH2.5 nel contratto di invio **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Fasi successive
* [Altre informazioni sui contratti degli account di integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  