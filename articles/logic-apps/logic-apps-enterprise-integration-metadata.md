---
title: Gestire i metadati degli elementi degli account di integrazione - App per la logica di Azure | Microsoft Docs
description: Aggiungere o recuperare i metadati degli elementi dagli account di integrazione nelle App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128804"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Gestire i metadati degli elementi dagli account di integrazione nelle App per la logica di Azure con Enterprise Integration Pack

È possibile definire i metadati personalizzati per gli elementi negli account di integrazione e recuperarli durante il runtime per l'app per la logica. Ad esempio, è possibile specificare i metadati per elementi quali partner, contratti, schemi e mappe. Si tratta in ogni caso di metadati archiviati tramite coppie chiave-valore. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Aggiungere metadati a elementi negli account di integrazione

1. Nel portale di Azure creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md).

2. Aggiungere un elemento all'account di integrazione, ad esempio, un [partner](logic-apps-enterprise-integration-partners.md), un [contratto](logic-apps-enterprise-integration-agreements.md) o uno [schema](logic-apps-enterprise-integration-schemas.md).

3. Selezionare l'elemento, scegliere **Modifica** e immettere i dettagli dei metadati.

   ![Immettere i metadati](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Recuperare metadati da elementi delle app per la logica

1. Nel portale di Azure creare un'[app per la logica](quickstart-create-first-logic-app-workflow.md).

2. Creare un [collegamento dall'app per la logica all'account di integrazione](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. Nella finestra di progettazione di app per la logica aggiungere un trigger come **richiesta** o **HTTP** all'app per la logica.

4. Nel trigger scegliere **Nuovo passaggio** > **Aggiungi un'azione**. Cercare "account di integrazione" in modo da trovare e quindi selezionare l'azione **Account di integrazione - Ricerca elemento dell'account di integrazione**.

   ![Selezionare Ricerca elemento dell'account di integrazione](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Selezionare un'opzione in **Tipo elemento** e specificare un nome in **Nome elemento**. Ad esempio: 

   ![Selezionare il tipo di elemento e specificare un nome elemento](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Esempio: recuperare i metadati del partner

Si supponga che al partner siano associati i metadati seguenti con i dettagli `routingUrl`:

![Cercare i metadati "routingURL" del partner](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Nell'app per la logica aggiungere il trigger, un'azione **Account di integrazione - Ricerca elemento dell'account di integrazione** per il partner e un'azione **HTTP**, ad esempio:

   ![Aggiungere trigger, ricerca dell'elemento e azione HTTP nell'app per la logica](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Per recuperare l'URI, sulla barra degli strumenti Progettazione app per la logica scegliere **Visualizzazione codice** per l'app per la logica. La definizione dell'app per la logica dovrebbe essere simile alla seguente:

   ![Ricerca](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui contratti](logic-apps-enterprise-integration-agreements.md)
