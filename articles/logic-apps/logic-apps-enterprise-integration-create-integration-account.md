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
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>Cos'è un account di integrazione?
Un account di integrazione consente alle app di integrazione aziendale di gestire elementi diversi, ad esempio schemi, mappe, certificati, partner e contratti. Qualsiasi applicazione di integrazione create usa un account di integrazione per accedere a tali schemi, mappe, certificati e così via.

## <a name="create-an-integration-account"></a>Creare un account di integrazione
1. Selezionare **Esplora**:   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati:     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. Selezionare il pulsante *Aggiungi* nel menu nella parte superiore della pagina:      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. Immettere il **nome**, selezionare la **sottoscrizione** da usare, creare un nuovo **gruppo di risorse** o selezionarne uno esistente, selezionare un **percorso** in cui verrà ospitato l'account di integrazione, un **piano tariffario** e quindi il pulsante **Crea**.   
   
   A questo punto verrà eseguito il provisioning dell'account di integrazione nella posizione selezionata. Questo dovrebbe essere completato entro 1 minuto.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Aggiornare la pagina. Il nuovo account di integrazione verrà visualizzato nell'elenco:  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Successivamente, collegare l'account di integrazione creato nell'app per la logica. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Collegare un account di integrazione a un'app per la logica
Per consentire alle app per la logica di accedere a mappe, schemi, contratti e altri elementi nell'account di integrazione, collegare quest'ultimo all'app per la logica.

#### <a name="prereqs"></a>Prerequisiti
* Un account di integrazione
* App per la logica

> [!NOTE] 
> Prima di iniziare, assicurarsi che l'account di integrazione e l'app per la logica si trovino nella **stessa posizione di Azure**.

1. Nel menu dell'app per la logica selezionare il collegamento **Impostazioni**:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. Nel pannello Impostazioni selezionare la voce **Account di integrazione**:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Nella casella di riepilogo a discesa **Selezionare un account di integrazione** selezionare l'account di integrazione da collegare all'app per la logica:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Salvare il lavoro:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Vine visualizzata una notifica con l'avviso che l'account di integrazione è stato collegato all'app per la logica e che tutti gli elementi nell'account di integrazione sono ora disponibili:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Ora che l'account di integrazione è collegato all'app per la logica, è possibile usare i connettori B2B presenti nelle app per la logica. Alcuni connettori B2B comuni includono la convalida di file XML e la codifica e decodifica di file flat.  

## <a name="how-to-delete-an-integration-account"></a>Come eliminare un account di integrazione?
1. Selezionare **Esplora**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selezionare l'**account di integrazione** da eliminare:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selezionare il collegamento **Elimina** nel menu:   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Confermare la scelta.    

## <a name="how-to-move-an-integration-account"></a>Come spostare un account di integrazione?
È possibile spostare facilmente un account di integrazione in una nuova sottoscrizione e in un nuovo gruppo di risorse. Per spostare l'account di integrazione seguire questi passaggi:

> [!IMPORTANT]
> Dopo lo spostamento di un account di integrazione è necessario aggiornare tutti gli script in modo che usino i nuovi ID risorsa.

1. Selezionare **Esplora**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selezionare l'**account di integrazione** da eliminare:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selezionare il collegamento **Sposta** nel menu:   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Confermare la scelta.    

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  




<!--HONumber=Jan17_HO5-->


