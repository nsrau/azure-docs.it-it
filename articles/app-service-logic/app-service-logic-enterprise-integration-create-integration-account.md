---
title: Panoramica degli account di integrazione ed Enterprise Integration Pack | Documentazione Microsoft
description: Tutte le informazioni sugli account di integrazione, Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8d4d5fa91a5644835034aac7f22b686500c93453


---
# <a name="overview-of-integration-accounts"></a>Panoramica degli account di integrazione
## <a name="what-is-an-integration-account"></a>Cos'è un account di integrazione?
Un account di integrazione è un account di Azure che consente alle applicazioni Enterprise Integration di gestire elementi quali schemi, mappe, certificati, partner e contratti. Qualsiasi applicazione Enterprise Integration creata dovrà usare un account di integrazione per poter accedere a un schema, mappa o certificato, ad esempio.

## <a name="create-an-integration-account"></a>Creare un account di integrazione
1. Selezionare **Esplora** .  
   ![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)  
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati.     
   ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)  
3. Selezionare il pulsante *Aggiungi* dal menu nella parte superiore della pagina.      
   ![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)  
4. Immettere il **nome**, selezionare la **sottoscrizione** da usare, creare un nuovo **gruppo di risorse** o selezionarne uno esistente, selezionare un **percorso** in cui verrà ospitato l'account di integrazione, un **piano tariffario** e quindi il pulsante **Crea**.   
   
   A questo punto verrà eseguito il provisioning dell'account di integrazione nella posizione selezionata. Questo dovrebbe essere completato entro 1 minuto.    
   ![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)  
5. Aggiornare la pagina. Il nuovo account di integrazione verrà visualizzato nell'elenco. Congratulazioni!  
   ![](./media/app-service-logic-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Come collegare un account di integrazione a un'app per la logica
Affinché le app per la logica possano accedere a mappe, schemi, contratti e altri elementi che si trovano nell'account di integrazione, è innanzitutto necessario collegare l'account di integrazione all'app per la logica.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>I passaggi per collegare un account di integrazione a un'app per la logica sono descritti di seguito
#### <a name="prerequisites"></a>Prerequisiti
* Un account di integrazione
* Un'app per la logica

> [!NOTE]
> Prima di iniziare assicurarsi che l'account di integrazione e l'app per la logica si trovino nella **stessa posizione di Azure**
> 
> 

1. Selezionare il collegamento **Impostazioni** dal menu dell'app per la logica  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)   
2. Selezionare la voce **Account di integrazione** dal pannello Impostazioni  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)   
3. Selezionare l'account di integrazione da collegare all'app per la logica dalla casella di riepilogo a discesa **Selezionare un account di integrazione**  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)   
4. Salvare il lavoro   
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)   
5. Verrà visualizzata una notifica con l'avviso che l'account di integrazione è stato collegato all'app per la logica e che tutti gli elementi nell'account di integrazione sono ora disponibili per l'app per la logica.  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)   

Ora che l'account di integrazione è collegato all'app per la logica, è possibile passare all'app per la logica e usare i connettori B2B, ad esempio la convalida XML, la codifica e decodifica di file flat o la trasformazione per creare app con funzionalità B2B.  

## <a name="how-to-delete-an-integration-account"></a>Come eliminare un account di integrazione?
1. Selezionare **Esplora**.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati.     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selezionare l'**account di integrazione** da eliminare.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Selezionare il collegamento **Elimina** nel menu.   
   ![](./media/app-service-logic-enterprise-integration-accounts/delete.png)  
5. Confermare la scelta    

## <a name="how-to-move-an-integration-account"></a>Come spostare un account di integrazione?
È possibile spostare facilmente un account di integrazione in una nuova sottoscrizione e in un nuovo gruppo di risorse. Per spostare l'account di integrazione seguire questi passaggi:

> [!IMPORTANT]
> Dopo lo spostamento di un account di integrazione è necessario aggiornare tutti gli script in modo che usino il nuovo ID risorsa.
> 
> 

1. Selezionare **Esplora**.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati.     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selezionare l'**account di integrazione** da eliminare.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Selezionare il collegamento **Sposta** nel menu.   
   ![](./media/app-service-logic-enterprise-integration-accounts/move.png)  
5. Confermare la scelta    

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](app-service-logic-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  




<!--HONumber=Nov16_HO3-->


