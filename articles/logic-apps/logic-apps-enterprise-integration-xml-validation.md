---
title: Panoramica della convalida XML ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni sul funzionamento della convalida in Enterprise Integration Pack e nelle app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 967d06e3df21aa1c194cdc81fb7cd0bdf53e82e4


---
# <a name="enterprise-integration-with-xml-validation"></a>Enterprise Integration con convalida XML
## <a name="overview"></a>Panoramica
Spesso negli scenari B2B i partner di un contratto devono accertarsi che i messaggi scambiati siano validi prima di avviare l'elaborazione dei dati. In Enterprise Integration Pack, è possibile usare il connettore di convalida XML per convalidare documenti in base a uno schema predefinito.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Come convalidare un documento con il connettore di convalida XML
1. Creare un'app per la logica e [collegarla all'account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene lo schema che verrà usato per convalidare i dati XML.
2. Aggiungere un trigger **Richiesta - Alla ricezione di una richiesta HTTP** all'app per la logica.  
   ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)    
3. Aggiungere l'azione **Convalida XML** selezionando prima di tutto **Aggiungi un'azione**  
4. Immettere *xml* nella casella di ricerca per filtrare tutte le azioni su quella che si desidera usare 
5. Selezionare **Convalida XML**     
   ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)   
6. Selezionare la casella di testo **CONTENUTO**  
   ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)
7. Selezionare il tag del corpo come contenuto che verrà convalidato.   
   ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)  
8. Selezionare la casella di riepilogo **NOME SCHEMA** e scegliere lo schema da usare per convalidare il *contenuto* immesso in precedenza     
   ![](./media/logic-apps-enterprise-integration-xml/xml-4.png) 
9. Salvare il lavoro   
   ![](./media/logic-apps-enterprise-integration-xml/xml-5.png) 

A questo punto, la configurazione del connettore di convalida è completa. In un'applicazione reale è possibile archiviare i dati convalidati in un'applicazione LOB, ad esempio SalesForce. È possibile eseguire facilmente questa azione inviando l'output della convalida a Salesforce. 

È ora possibile testare l'azione di convalida eseguendo una richiesta all'endpoint HTTP.  

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")   




<!--HONumber=Jan17_HO3-->


