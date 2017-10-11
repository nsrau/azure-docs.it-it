---
title: La decodifica edifact delle app per la logica B2B risolve UNH2.5 - App per la logica di Azure | Microsoft Docs
description: La decodifica edifact delle app per la logica di Azure B2B risolve UNH2.5
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Come gestire documenti EDIFACT con un segmento UNH2.5
Quando nel documento EDIFACT è presente un segmento UNH 2.5, viene usato per la ricerca dello schema. 

Esempio: il campo UNH è **EAN008** nel messaggio EDIFACT  
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

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti degli account di integrazione](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  