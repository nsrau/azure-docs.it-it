---
title: Creare, collegare, eliminare o spostare un account di integrazione in App per la logica di Azure | Documentazione Microsoft
description: Come creare un account di integrazione e collegarlo ad app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 716e7b5bab8725dea0fd2b760d0e46e8e892c5b4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-an-integration-account"></a>Cos'è un account di integrazione?

Un account di integrazione consente alle app di integrazione aziendale di gestire elementi diversi, ad esempio schemi, mappe, certificati, partner e contratti. Qualsiasi applicazione di integrazione create usa un account di integrazione per accedere a tali schemi, mappe, certificati e così via.

## <a name="create-an-integration-account"></a>Creare un account di integrazione

1.  Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure"). Fare clic su **Altri servizi** nel menu a sinistra.

    ![Selezionare "Altri servizi"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Nella casella di ricerca, digitare "integrazione" come filtro. Nell'elenco dei risultati selezionare **Account di integrazione**.

    ![Filtro su "integrazione", selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Nella parte superiore della pagina fare clic su **Aggiungi**.

    ![Scegliere Aggiungi](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Denominare l'account di integrazione e selezionare la sottoscrizione di Azure da usare. È possibile creare un nuovo **Gruppo di risorse** o selezionarne uno esistente. Selezionare quindi la **località** in cui eseguire l'hosting dell'account di integrazione e un **piano tariffario**. 

    Al termine, scegliere **Crea**.

    ![Immettere i dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    Azure esegue il provisioning dell'account di integrazione nella località selezionata. L'esecuzione dell'operazione dura circa 1 minuto.

5. Aggiornare la pagina. Il nuovo account di integrazione verrà visualizzato nell'elenco.

    ![Visualizzazione del nuovo account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Collegare quindi l'account di integrazione creato all'app per la logica. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Collegare un account di integrazione a un'app per la logica

Per consentire alle app per la logica di accedere a mappe, schemi, contratti e altri elementi nell'account di integrazione, collegare quest'ultimo all'app per la logica.

### <a name="prerequisites"></a>Prerequisiti

* Un account di integrazione
* App per la logica

> [!NOTE] 
> Prima di iniziare, assicurarsi che l'account di integrazione e l'app per la logica si trovino nella *stessa posizione di Azure*.


1. Nel portale di Azure, selezionare l'app per la logica e controllarne la località.

    ![Selezionare l'app per la logica, controllare la località](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. In **Impostazioni**, selezionare **Account di integrazione**.

    ![Selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Nell'elenco**Selezionare un account di integrazione** selezionare l'account di integrazione da collegare all'app per la logica. Per completare il collegamento, selezionare **Salva**.

    ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    Viene visualizzata una notifica con l'avviso che l'account di integrazione è stato collegato all'app per la logica e che tutti gli elementi nell'account di integrazione sono ora disponibili.

    ![L'app per la logica è collegata all'account di integrazione.](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Ora che l'account di integrazione è collegato all'app per la logica, è possibile usare i connettori B2B presenti nelle app per la logica. Alcuni connettori B2B comuni includono la convalida XML e la codifica e decodifica di file flat.  

## <a name="delete-your-integration-account"></a>Eliminare l'account di integrazione

1. Selezionare **Altri servizi**.

    ![Selezionare "Altri servizi"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Nella casella di ricerca, digitare "integrazione" come filtro. Nell'elenco dei risultati selezionare **Account di integrazione**.

    ![Filtro su "integrazione", selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Selezionare l'account di integrazione da eliminare.

    ![Selezionare l'account di integrazione da eliminare](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. Dal menu, scegliere **Elimina**.

    ![Scegliere "Elimina"](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. Confermare la scelta di eliminazione dell'account di integrazione.

## <a name="move-your-integration-account"></a>Spostare l'account di integrazione

Per spostare un account di integrazione a un'altra sottoscrizione o gruppo di risorse di Azure, seguire questa procedura.

> [!IMPORTANT]
> Dopo lo spostamento di un account di integrazione è necessario aggiornare tutti gli script in modo che usino i nuovi ID risorsa.

1. Selezionare **Altri servizi**.

    ![Selezionare "Altri servizi"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Nella casella di ricerca, digitare "integrazione" come filtro. Nell'elenco dei risultati selezionare **Account di integrazione**.

    ![Filtro su "integrazione", selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. Selezionare l'account di integrazione da spostare. In **Impostazioni**, scegliere **Proprietà**.

    ![Selezionare l'account di integrazione da spostare. In Impostazioni, scegliere Proprietà](./media/logic-apps-enterprise-integration-accounts/move.png)

5. Modificare il gruppo di risorse o la sottoscrizione di Azure associati all'account di integrazione.

    ![Scegliere Cambia il gruppo di risorse o Cambia sottoscrizione](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  

