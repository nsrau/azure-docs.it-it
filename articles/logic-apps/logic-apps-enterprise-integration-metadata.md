---
title: Metadati dell&quot;account di integrazione delle app per la logica di Azure | Microsoft Docs
description: Panoramica dei metadati dell&quot;account di integrazione
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
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Metadati dell'account di integrazione delle app per la logica di Azure

## <a name="overview"></a>Panoramica

Partner, contratti, schemi e mappe aggiunti a un account di integrazione, metadati archiviati con coppie chiave-valore. È possibile definire i metadati personalizzati, che possono essere recuperati durante il runtime.  Al momento gli elementi non dispongono di funzionalità per creare metadati nell'interfaccia utente, ma è possibile usare le API REST a tal proposito.  Partner, contratti e schema necessario **MODIFICA come JSON** e consentono di digitare le informazioni sui metadati.  In un'app per la logica, **Integration Account Artifact LookUp** (Ricerca elemento account di integrazione) consente di recuperare le informazioni relative ai metadati.

## <a name="how-to-store-metadata"></a>Come archiviare i metadati

1. Creare un [account di integrazione](logic-apps-enterprise-integration-create-integration-account.md)   

2. Aggiungere un [partner](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner) o [contratto](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements), oppure uno [schema](logic-apps-enterprise-integration-schemas.md) all'account di integrazione

3. Selezionare un parter o contratto, oppure uno schema. Selezionare **Modifica come JSON** e immettere i dettagli dei metadati    
![Immettere i metadati](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Chiamare **Integration Account Artifact LookUp** (Ricerca elemento account di integrazione) da un'app per la logica

1. Creare un'[app per la logica](logic-apps-create-a-logic-app.md)

2. [Collegare](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app) l'app per la logica a un account di integrazione    

3. Creare un trigger, ad esempio usando *Richiesta* o *HTTP* prima di cercare **Integration Account Artifact LookUp** (Ricerca elemento account di integrazione).  Cercare il termine **integrazione** per eseguire la ricerca di **Integration Account Artifact LookUp** (Ricerca elemento account di integrazione)  
![Ricerca](media/logic-apps-enterprise-integration-metadata/image2.png)

3. Selezione **Integration Account Artifact LookUp** (Ricerca elemento account di integrazione)  

4. Selezionare **Tipo elemento** e specificare un **Nome elemento**  
![Ricerca](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>Un esempio per recuperare i metadati del partner

1. I metadati del partner dispongono di dettagli URL di routing    
![Ricerca](media/logic-apps-enterprise-integration-metadata/image6.png)

2. In un'app per la logica configurare **Integration Account Artifact LookUp** (Ricerca elemento account di integrazione) e **HTTP**   
![Ricerca](media/logic-apps-enterprise-integration-metadata/image4.png)

3. Per recuperare l'URI, la visualizzazione del codice deve essere simile a    
![Ricerca](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  



<!--HONumber=Feb17_HO1-->


