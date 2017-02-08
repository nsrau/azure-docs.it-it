---
title: Panoramica delle mappe di Enterprise Integration Pack | Documentazione Microsoft
description: Informazioni su come usare le mappe con Enterprise Integration Pack e le app per la logica
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 7e95b4da32aee892bf08cbcbe23a67d110911bf6


---
# <a name="learn-about-maps-and-the-enterprise-integration-pack"></a>Informazioni sulle mappe ed Enterprise Integration Pack
## <a name="overview"></a>Panoramica
Enterprise Integration usa le mappe per trasformare i dati XML da un formato all'altro. 

## <a name="what-is-a-map"></a>Che cos'è una mappa?
Una mappa è un documento XML che definisce il modo in cui i dati di un documento devono essere trasformati in un altro formato. 

## <a name="why-use-maps"></a>Perché usare le mappe?
Si supponga di ricevere regolarmente fatture o ordini B2B da un partner che usa il formato AAAMMGG per le date. In azienda però le date vengono archiviare nel formato MMGGAAA. È possibile usare una mappa per *trasformare* il formato di data AAAMMGG in MMGGAAA prima di archiviare i dettagli dell'ordine o della fattura nel database relativo.

## <a name="how-do-i-create-a-map"></a>Come si crea una mappa?
[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") per Visual Studio 2015 consente di creare progetti di integrazione di Biztalk.  La creazione di un file di mappa di integrazione consente di mappare visivamente gli elementi tra due file di schema XML.  Dopo aver compilato il progetto, viene prodotto un documento XSLT.

## <a name="how-to-upload-a-map"></a>Come caricare una mappa?
Nel portale di Azure:  

1. Selezionare **Esplora**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Immettere **integrazione** nella casella di ricerca del filtro e selezionare **Account di integrazione** nell'elenco dei risultati.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selezionare l'**account di integrazione** a cui aggiungere la mappa  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selezionare il riquadro **Mappe**  
   ![](./media/logic-apps-enterprise-integration-maps/map-1.png)  
5. Selezionare il pulsante **Aggiungi** nel pannello Mappe visualizzato  
   ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  
6. Immettere il **nome** per la mappa, quindi selezionare l'icona della cartella sul lato destro della casella di testo **Mappa** per caricare il file della mappa. Dopo aver completato il processo di caricamento, selezionare il pulsante **OK** .  
   ![](./media/logic-apps-enterprise-integration-maps/map-3.png)  
7. La mappa viene aggiunta all'account di integrazione. Verrà visualizzata una notifica sullo schermo con l'avviso dell'esito positivo o negativo dell'aggiunta del file di mappa. Dopo aver ricevuto la notifica, selezionare il riquadro **Mappe** per visualizzare la mappa appena aggiunta nel pannello Mappe:    
   ![](./media/logic-apps-enterprise-integration-maps/map-4.png)  

## <a name="how-to-edit-a-map"></a>Come modificare una mappa?
Per modificare una mappa, è necessario caricare un nuovo file di mappa con le modifiche desiderate. È anche possibile scaricare la mappa e poi modificarla. 

Per caricare una nuova mappa che sostituisce una mappa esistente, seguire questi passaggi:  

1. Selezionare il riquadro **Mappe**  
2. Quando si apre il pannello Mappe selezionare la mappa da modificare  
3. Nel pannello **Mappe** selezionare il collegamento **Aggiorna**  
   ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)   
4. Selezionare il file di mappa da caricare usando la finestra di dialogo di selezione file visualizzata, quindi selezionare **Apri**   
   ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)   
5. Dopo il caricamento della mappa verrà visualizzato un popup di notifica.    

## <a name="how-to-delete-a-map"></a>Come eliminare una mappa?
1. Selezionare il riquadro **Mappe**  
2. Quando si apre il pannello Mappe selezionare la mappa da eliminare  
3. Selezionare il collegamento **Elimina**.    
   ![](./media/logic-apps-enterprise-integration-maps/delete.png)   
4. Confermare che si intende eliminare la mappa.  
   ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)   

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  
* [Altre informazioni sulle trasformazioni](logic-apps-enterprise-integration-transform.md "Informazioni sulle trasformazioni di Enterprise Integration")  




<!--HONumber=Jan17_HO3-->


