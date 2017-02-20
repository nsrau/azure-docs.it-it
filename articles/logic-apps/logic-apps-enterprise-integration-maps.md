---
title: Trasformare i dati XML con le mappe XSLT - App per la logica di Azure | Microsoft Docs
description: Aggiungere mappe XSLT per trasformare i dati XML con App per la logica di Azure ed Enterprise Integration Pack
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
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 7729890157900d0211b3a7ec05096ca315018875
ms.openlocfilehash: 23d79fb83c1c4b103407c001dcafb8b1f3cfa5a7


---
# <a name="add-maps-for-xml-data-transform"></a>Aggiungere mappe per la trasformazione dei dati XML

Enterprise Integration usa mappe per trasformare i dati XML da un formato all'altro. Una mappa è un documento XML che definisce i dati di un documento che devono essere trasformati in un altro formato. 

## <a name="why-use-maps"></a>Perché usare le mappe?

Si supponga di ricevere regolarmente fatture o ordini B2B da un cliente che usa il formato AAAMMGG per le date. In azienda però le date vengono archiviare nel formato MMGGAAA. È possibile usare una mappa per *trasformare* il formato di data AAAMMGG in MMGGAAA prima di archiviare i dettagli dell'ordine o della fattura nel database relativo.

## <a name="how-do-i-create-a-map"></a>Come si crea una mappa?

È possibile creare progetti BizTalk Integration con [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") per Visual Studio 2015. È quindi possibile creare un file Integration Map che consente di eseguire il mapping visivo degli elementi tra due file di schema XML. Dopo aver compilato il progetto, si disporrà di un documento XSLT.

## <a name="how-do-i-add-a-map"></a>Come si aggiunge una mappa?

1. Nel Portale di Azure selezionare **Sfoglia**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Nella casella di ricerca per filtrare immettere **integrazione**, quindi selezionare **Account di integrazione** dall'elenco dei risultati.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Selezionare l'account di integrazione in cui si desidera aggiungere la mappa.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Selezionare il riquadro **Mappe**.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Quando il pannello Mappe è aperto, scegliere **Aggiungi**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Immettere un **nome** per la mappa. Per caricare il file di mappa, scegliere l'icona a forma di cartella a destra della casella di testo **Mappa**. Al termine del processo di caricamento, scegliere **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Dopo che Azure aggiunge la mappa all'account di integrazione, viene visualizzato un messaggio che indica se il file di mappa è stato aggiunto. Dopo aver visualizzato il messaggio, scegliere il riquadro **Mappe** in modo da visualizzare la mappa appena aggiunta.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)

## <a name="how-do-i-edit-a-map"></a>Come si modifica una mappa?

È necessario caricare un nuovo file di mappa con le modifiche desiderate. È anche possibile scaricare la mappa e poi modificarla.

Per caricare una nuova mappa che sostituisce una mappa esistente, attenersi ai passaggi seguenti.

1. Scegliere il riquadro **Mappe**.

2. Quando il pannello Mappe è aperto, selezionare la mappa da modificare.

3. Nel pannello **Mappe** scegliere **Aggiorna**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Nella selezione file selezionare il file di mappa da caricare, quindi scegliere **Apri**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Come eliminare una mappa?

1. Scegliere il riquadro **Mappe**.

2. Quando il pannello Mappe è aperto, selezionare la mappa da eliminare.

3. Scegliere **Elimina**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Confermare che si desidera eliminare la mappa.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  
* [Altre informazioni sulle trasformazioni](logic-apps-enterprise-integration-transform.md "Informazioni sulle trasformazioni di Enterprise Integration")  




<!--HONumber=Feb17_HO1-->


