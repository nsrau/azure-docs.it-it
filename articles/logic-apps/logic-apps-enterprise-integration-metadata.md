---
title: Gestire i metadati degli elementi degli account di integrazione - App per la logica di Azure | Microsoft Docs
description: Aggiungere o recuperare i metadati degli elementi dagli account di integrazione per le app per la logica di Azure
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 28bb8296ddd820ec5aa9793dc0928b4b1e67bf6f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Gestire i metadati degli elementi dagli account di integrazione per le app per la logica

È possibile definire i metadati personalizzati per gli elementi negli account di integrazione e recuperarli durante il runtime per l'app per la logica. Ad esempio, è possibile specificare i metadati per elementi quali partner, contratti, schemi e mappe; in tutti i casi i metadati vengono archiviati con coppie chiave-valore. Attualmente, gli elementi non possono creare metadati tramite interfaccia utente, ma è possibile usare le API REST per creare i metadati. Per aggiungere metadati quando si crea o si seleziona un partner, un contratto o uno schema nel Portale di Azure, scegliere **Modifica come JSON**. Per recuperare i metadati degli elementi nelle app per la logica, è possibile usare la funzionalità di ricerca elemento dell'account di integrazione.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Aggiungere metadati a elementi negli account di integrazione

1. Creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md).

2. Aggiungere un elemento all'account di integrazione, ad esempio, un [partner](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), un [contratto](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) o uno [schema](logic-apps-enterprise-integration-schemas.md).

3.  Selezionare l'elemento, scegliere **Modifica come JSON** e immettere i dettagli dei metadati.

    ![Immettere i metadati](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Recuperare metadati da elementi delle app per la logica

1. Creare un'[app per la logica](logic-apps-create-a-logic-app.md).

2. Creare un [collegamento dall'app per la logica all'account di integrazione](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. Nella finestra di progettazione di app per la logica aggiungere un trigger come *richiesta* o *HTTP* all'app per la logica.

4.  Scegliere **Passaggio successivo** > **Aggiungi un'azione**. Cercare *integrazione* in modo da trovare e quindi selezionare **Account di integrazione - Ricerca elemento dell'account di integrazione**.

    ![Selezionare Ricerca elemento dell'account di integrazione](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Selezionare il **tipo di elemento** e specificare un **nome elemento**

    ![Selezionare il tipo di elemento e specificare un nome elemento](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Esempio: recuperare i metadati del partner

I metadati del partner sono caratterizzati dai seguenti dettagli `routingUrl`:

![Cercare i metadati "routingURL" del partner](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Nell'app per la logica aggiungere, il trigger, un'azione **Account di integrazione - Ricerca elemento dell'account di integrazione** per il partner e un **HTTP**.

    ![Aggiungere trigger, ricerca di elementi e "HTTP" all'app per la logica](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Per recuperare l'URI, passare alla visualizzazione Codice per app per la logica. La definizione dell'app per la logica dovrebbe essere simile alla seguente:

    ![Ricerca](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  
