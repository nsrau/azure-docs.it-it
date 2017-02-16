---
title: Informazioni sui partner ed Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare i partner con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>Informazioni sui partner ed Enterprise Integration Pack
## <a name="overview"></a>Panoramica
Prima di poter creare un partner, l'utente e l'organizzazione con cui si intende intrattenere attività commerciali devono condividere informazioni che consentiranno di identificare e convalidare i messaggi scambiati. Dopo avere completato questa procedura, è possibile avviare le relazioni aziendali e creare un *partner* nell'account di integrazione.

## <a name="what-is-a-partner"></a>Informazioni sui partner
I partner sono le entità che partecipano alle transazioni e ai messaggi Business-To-Business (B2B). 

## <a name="how-are-partners-used"></a>Uso dei partner
I partner vengono usati per creare i contratti. Un contratto definisce i dettagli relativi ai messaggi che verranno scambiati tra i partner. 

Prima di creare un contratto è necessario aggiungere almeno due partner all'account di integrazione. Uno dei partner del contratto deve essere l'organizzazione dell'utente. Il partner che rappresenta l'organizzazione è definito come **partner host**. Il secondo partner rappresenta l'organizzazione con cui l'organizzazione dell'utente scambia messaggi. Il secondo partner viene definito **partner guest**. Il partner guest può essere un'altra società o anche un reparto all'interno della stessa organizzazione dell'utente.  

Dopo aver aggiunto i partner, usarli per creare un contratto. 

Le impostazioni per la ricezione e l'invio sono configurate dal punto di vista del partner host. Le impostazioni di ricezione del contratto, ad esempio, determinano la modalità con cui il partner host riceve i messaggi inviati da un partner guest. Analogamente, le impostazioni di invio del contratto indicano in che modo il partner host invia messaggi al partner guest.

## <a name="how-to-create-a-partner"></a>Procedura: Creare un partner
Nel portale di Azure:  

1. Selezionare **Esplora**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selezionare l'**account di integrazione** a cui aggiungere i partner.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selezionare il riquadro **Partner**  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. Selezionare il pulsante **Aggiungi** nel pannello Partner che viene aperto.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. Immettere il **Nome**, quindi selezionare **Qualificatore** e immettere il **Valore**. Il valore viene usato per identificare i documenti ricevuti dalle app.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. Selezionare l'icona di notifica a forma di *campana* per visualizzare lo stato di avanzamento del processo di creazione del partner.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. Selezionare il riquadro **Partner** . Questa operazione aggiorna il riquadro e consente la visualizzazione del numero progressivo del partner, che indica che il nuovo partner è stato aggiunto correttamente.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. Dopo aver selezionato il riquadro Partner, anche il partner appena aggiunto viene visualizzato nel pannello.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>Procedura: Come modificare un partner
Seguire la procedura in basso per modificare un partner già esistente nell'account di integrazione:  

1. Selezionare il riquadro **Partner**  
2. Selezionare il partner che si desidera modificare quando si apre il pannello Partner  
3. Nel riquadro **Aggiorna partner** apportare le modifiche necessarie  
4. Se si è soddisfatti delle modifiche, selezionare il collegamento **Salva** o, in caso contrario, scegliere il collegamento **Ignora** per eliminare le modifiche.  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>Procedura: Eliminare un partner
1. Selezionare il riquadro **Partner**  
2. Selezionare il partner che si desidera modificare quando si apre il pannello Partner  
3. Selezionare il collegamento **Elimina**.    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  




<!--HONumber=Jan17_HO3-->


