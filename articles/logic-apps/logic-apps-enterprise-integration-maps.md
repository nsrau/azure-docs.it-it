---
title: Trasformare i dati XML con le mappe XSLT - App per la logica di Azure | Microsoft Docs
description: Aggiungere mappe XSLT che trasformano i dati XML in App per la logica di Azure con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 07/08/2016
ms.openlocfilehash: c5e5e0a0a3f8bd5feedc00d5bbfb76a1453ccc84
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123557"
---
# <a name="add-maps-for-xml-transformation-in-azure-logic-apps-with-enterprise-integration-pack"></a>Aggiungere mappe per la trasformazione dei dati XML in App per la logica di Azure con Enterprise Integration Pack

Enterprise Integration usa mappe per trasformare i dati XML da un formato all'altro. Una mappa è un documento XML che definisce i dati di un documento che devono essere trasformati in un altro formato. 

## <a name="why-use-maps"></a>Perché usare le mappe?

Si supponga di ricevere regolarmente fatture o ordini B2B da un cliente che usa il formato AAAMMGG per le date. In azienda però le date vengono archiviare nel formato MMGGAAA. È possibile usare una mappa per *trasformare* il formato di data AAAMMGG in MMGGAAA prima di archiviare i dettagli dell'ordine o della fattura nel database relativo.


## <a name="how-do-i-create-a-map"></a>Come si crea una mappa?

È possibile creare progetti BizTalk Integration con [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack") per Visual Studio 2015. È quindi possibile creare un file Integration Map che consente di eseguire il mapping visivo degli elementi tra due file di schema XML. Dopo aver compilato il progetto, si disporrà di un documento XSLT.

Se una mappa ha un riferimento a un assembly esterno, allora entrambi devono essere caricati per l’account di integrazione. Devono essere caricati in un ordine specifico, innanzitutto l'assembly e quindi la mappa che fa riferimento all'assembly.


## <a name="how-do-i-add-a-map"></a>Come si aggiunge una mappa?

1. Nel Portale di Azure selezionare **Sfoglia**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Nella casella di ricerca per filtrare immettere **integrazione**, quindi selezionare **Account di integrazione** dall'elenco dei risultati.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Selezionare l'account di integrazione in cui si desidera aggiungere la mappa.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Selezionare il riquadro **Mappe**.

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. Quando la pagina Mappe è aperta, scegliere **Aggiungi**.

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. Immettere un **nome** per la mappa. Per caricare il file di mappa, scegliere l'icona a forma di cartella a destra della casella di testo **Mappa**. Al termine del processo di caricamento, scegliere **OK**.

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. Dopo che Azure aggiunge la mappa all'account di integrazione, viene visualizzato un messaggio che indica se il file di mappa è stato aggiunto. Dopo aver visualizzato il messaggio, scegliere il riquadro **Mappe** in modo da visualizzare la mappa appena aggiunta.

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)


## <a name="how-do-i-add-an-assembly"></a>Come si aggiunge un account?
Aprire l'account di integrazione dove si desidera aggiungere l’assembly.

1. Scegliere il riquadro degli **Assembly**.

    ![Riquadro di assembly per l’account di integrazione](./media/logic-apps-enterprise-integration-maps/assemblytile.png)

2. Quando la pagina degli assembly è aperta, scegliere **Aggiungi**. Immettere un **Nome** per l'assembly. Per caricare il file di assembly, scegliere l'icona a forma di cartella a destra della casella di testo **Assembly** . Al termine del processo di caricamento, scegliere **OK**.

    ![Aggiungi assembly](./media/logic-apps-enterprise-integration-maps/assemblyfile.png)


## <a name="how-do-i-edit-a-map"></a>Come si modifica una mappa?

È necessario caricare un nuovo file di mappa con le modifiche desiderate. È anche possibile scaricare la mappa e poi modificarla.

Per caricare una nuova mappa che sostituisce una mappa esistente, attenersi ai passaggi seguenti.

1. Scegliere il riquadro **Mappe**.

2. Quando la pagina Mappe è aperta, selezionare la mappa che si desidera modificare.

3. Nella pagina **Mappe** scegliere **Aggiorna**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. Nella selezione file selezionare il file di mappa da caricare, quindi scegliere **Apri**.

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>Come eliminare una mappa?

1. Scegliere il riquadro **Mappe**.

2. Quando la pagina Mappe è aperta, selezionare la mappa che desideri eliminare.

3. Scegliere **Elimina**.

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. Confermare che si desidera eliminare la mappa.

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
* [Altre informazioni sui contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")  
* [Altre informazioni sulle trasformazioni](logic-apps-enterprise-integration-transform.md "Informazioni sulle trasformazioni di Enterprise Integration")  

