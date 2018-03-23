---
title: Creare, collegare, eliminare o spostare un account di integrazione in App per la logica di Azure | Documentazione Microsoft
description: Come creare un account di integrazione e collegarlo ad app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; divswa
ms.openlocfilehash: fb1d0ceb26c5ed792f22051e2af10a7572200bdc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="what-is-an-integration-account"></a>Cos'è un account di integrazione?

Un account di integrazione consente alle app di integrazione aziendali, in particolare le app per la logica, di accedere a elementi B2B, quali partner commerciali, contratti, mappe, schemi, certificati e così via, ed eseguirne la gestione. Per fornire l'accesso, collegare l'account di integrazione all'app per la logica, dopo aver verificato che entrambi si trovino nello *stesso percorso Azure*.

## <a name="create-an-integration-account"></a>Creare un account di integrazione

1. Accedere al [Portale di Azure](http://portal.azure.com "Portale di Azure"). 

2. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.

   ![Creare un account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. Nella parte superiore della pagina fare clic su **Aggiungi**.

   ![Scegliere Aggiungi](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Denominare l'account di integrazione e selezionare la sottoscrizione di Azure da usare. È possibile creare un nuovo **Gruppo di risorse** o selezionarne uno esistente. Selezionare un **percorso** in cui eseguire l'hosting dell'account di integrazione e un **piano tariffario**. Al termine, scegliere **Crea**.

   ![Immettere i dettagli dell'account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure esegue il provisioning dell'account di integrazione nel percorso selezionato. L'esecuzione dell'operazione dura circa un minuto.

5. Aggiornare la pagina. Il nuovo account di integrazione verrà visualizzato nell'elenco.

   ![Visualizzazione del nuovo account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Collegare quindi l'account di integrazione creato all'app per la logica. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Collegare un account di integrazione a un'app per la logica

Per consentire alle app per la logica di accedere a elementi B2B, quali partner commerciali, contratti, mappe e schemi nell'account di integrazione, collegare quest'ultimo all'app per la logica. 

1. Nel portale di Azure, selezionare l'app per la logica e controllarne la località.

   ![Selezionare l'app per la logica, controllare la località](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. In **Impostazioni**, selezionare **Account di integrazione**.

   ![Selezionare "Account di integrazione"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Nell'elenco**Selezionare un account di integrazione** selezionare l'account di integrazione da collegare all'app per la logica. Per completare il collegamento, selezionare **Salva**.

   ![Selezionare l'account di integrazione](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Viene visualizzata una notifica con l'avviso che l'account di integrazione è stato collegato all'app per la logica e che tutti gli elementi nell'account di integrazione sono ora disponibili.

   ![L'app per la logica è collegata all'account di integrazione.](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Ora che l'account di integrazione è collegato all'app per la logica, è possibile usare i connettori B2B presenti nell'app per la logica. Alcuni connettori B2B comuni includono la convalida XML e la codifica e decodifica di file flat.  

## <a name="delete-your-integration-account"></a>Eliminare l'account di integrazione

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Selezionare l'account di integrazione da eliminare.

    ![Selezionare l'account di integrazione da eliminare](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. Dal menu, scegliere **Elimina**.

    ![Scegliere "Elimina"](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Confermare la scelta di eliminazione dell'account di integrazione.

## <a name="move-your-integration-account"></a>Spostare l'account di integrazione

Per spostare un account di integrazione a un'altra sottoscrizione o gruppo di risorse di Azure, seguire questa procedura. Dopo lo spostamento dell'account di integrazione, è necessario aggiornare tutti gli script in modo che usino i nuovi ID risorsa.

1. Nel menu principale di Azure selezionare **Tutti i servizi**. Nella casella di ricerca immettere "integrazione" e quindi selezionare **Account di integrazione**.

   ![Trovare l'account di integrazione](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Selezionare l'account di integrazione da spostare. In **Impostazioni**, scegliere **Proprietà**.

   ![Selezionare l'account di integrazione da spostare. In Impostazioni, scegliere Proprietà](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Modificare il gruppo di risorse o la sottoscrizione di Azure associati all'account di integrazione.

   ![Scegliere Cambia il gruppo di risorse o Cambia sottoscrizione](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  

