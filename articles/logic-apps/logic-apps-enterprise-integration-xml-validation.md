---
title: Convalida XML - App per la logica di Azure | Microsoft Docs
description: Convalida XML con gli schemi per le app per la logica di Azure e negli scenari B2B usando Enterprise Integration Pack
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
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 03cd3f4edd7bb7895efa02475411d813ef44b8b3
ms.openlocfilehash: ae0bb2cb090402f61bb10299e50024f1f2938489


---
# <a name="validate-xml-for-enterprise-integration"></a>Enterprise Integration con convalida XML

Spesso negli scenari B2B i partner di un contratto devono accertarsi che i messaggi scambiati siano validi prima di avviare l'elaborazione dei dati. In Enterprise Integration Pack, è possibile usare il connettore di convalida XML per convalidare i documenti in base a uno schema predefinito.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Come convalidare un documento con il connettore di convalida XML

1. Creare un'app per la logica e [collegarla all'account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene lo schema che verrà usato per convalidare i dati XML.

2. Aggiungere un trigger **Richiesta - Alla ricezione di una richiesta HTTP** all'app per la logica.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. Aggiungere l'azione **Convalida XML** selezionando **Aggiungi un'azione**.

4. Immettere *xml* nella casella di ricerca per filtrare tutte le azioni su quella che si desidera. Selezionare **Convalida XML**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. Per specificare il contenuto XML che si desidera convalidare, selezionare **CONTENUTO**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. Selezionare il tag del corpo come contenuto che si desidera convalidare.

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. Per specificare lo schema che desidera usare per convalidare il *contenuto* immesso in precedenza, scegliere **NOME SCHEMA**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. Salvare il lavoro   

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

A questo punto, la configurazione del connettore di convalida è completa. In un'applicazione reale è possibile archiviare i dati convalidati in un'app line-of-business (LOB), ad esempio SalesForce. Per inviare l'output della convalida a Salesforce, aggiungere un'azione.

Per testare l'azione di convalida, eseguire una richiesta all'endpoint HTTP.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")   




<!--HONumber=Feb17_HO1-->


