---
title: Visualizzare i dati usando i dashboard basati su Cartelle di lavoro di Monitoraggio di Azure in Azure Sentinel. Documenti Microsoft
description: Usare questa esercitazione per informazioni su come visualizzare i dati usando dashboard basati su cartelle di lavoro in Azure Sentinel.Use this tutorial to learn how to visualize your data using dashboards based on workbooks in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585221"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Esercitazione: Visualizzare e monitorare i datiTutorial: Visualize and monitor your data



Dopo aver [connesso le origini](quickstart-onboard.md) dati ad Azure Sentinel, è possibile visualizzare e monitorare i dati usando l'adozione di Azure Sentinel delle cartelle di lavoro di Monitoraggio di Azure, che offre versatilità nella creazione di dashboard personalizzati. Mentre le cartelle di lavoro vengono visualizzate in modo diverso in Azure Sentinel, può essere utile vedere come [creare report interattivi con le](../azure-monitor/app/usage-workbooks.md)cartelle di lavoro di Monitoraggio di Azure. Azure Sentinel consente di creare cartelle di lavoro personalizzate tra i dati e include anche modelli di cartella di lavoro predefiniti per ottenere rapidamente informazioni dettagliate sui dati non appena si connette un'origine dati.


Questa esercitazione consente di visualizzare i dati in Azure Sentinel.This tutorial helps you visualize your data in Azure Sentinel.
> [!div class="checklist"]
> * Usare cartelle di lavoro predefinite
> * Creare nuove cartelle di lavoro

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre almeno delle autorizzazioni Lettore di cartelle o Collaboratore cartella di lavoro per il gruppo di risorse dell'area di lavoro di Azure Sentinel.You must have almeno Workbook reader or Workbook contributor permissions on the resource group of the Azure Sentinel workspace.

> [!NOTE]
> Le cartelle di lavoro che è possibile visualizzare in Azure Sentinel vengono salvate all'interno del gruppo di risorse dell'area di lavoro di Azure Sentinel e sono contrassegnate dall'area di lavoro in cui sono state create.

## <a name="use-built-in-workbooks"></a>Usare cartelle di lavoro predefinite

1. Passare a Cartelle di lavoro e quindi selezionare Modelli per visualizzare l'elenco completo delle cartelle di lavoro predefinite di Azure Sentinel.Go to **Workbooks** and then select **Templates** to see the full list of Azure Sentinel built-in workbooks. Per vedere quali sono rilevanti per i tipi di dati connessi, il campo Tipi di **dati obbligatori** in ogni cartella di lavoro elencherà il tipo di dati accanto a un segno di spunta verde se si trasmettono già i dati rilevanti in Azurenel.
  ![passare alle cartelle di lavoro](./media/tutorial-monitor-data/access-workbooks.png)
1. Fare clic su **Visualizza cartella di lavoro** per visualizzare il modello popolato con i dati.
  
1. Per modificare la cartella di lavoro, selezionare **Salva**e quindi selezionare il percorso in cui si desidera salvare il file json per il modello. 

   > [!NOTE]
   > In questo modo viene creata una risorsa di Azure basata sul modello pertinente e viene salvato il file Json del modello stesso e non i dati.


1. Selezionare **Visualizza cartella di lavoro**. Quindi, fare clic sul pulsante **Modifica** nella parte superiore. È ora possibile modificare la cartella di lavoro e personalizzarla in base alle proprie esigenze. Per altre informazioni su come personalizzare la cartella di lavoro, vedere Creazione di report interattivi con le cartelle di lavoro di Monitoraggio di Azure.For more information on how to customize the workbook, see how to [Create interactive reports with Azure Monitor Workbooks](../azure-monitor/app/usage-workbooks.md).
![visualizzare cartelle di lavoro](./media/tutorial-monitor-data/workbook-graph.png)
1. Dopo aver apportato le modifiche, è possibile salvare la cartella di lavoro. 

1. È anche possibile clonare la cartella di lavoro: selezionare **Modifica** e quindi **Salva con nome**, assicurandosi di salvarla con un altro nome, nella stessa sottoscrizione e nello stesso gruppo di risorse. Queste cartelle di lavoro vengono visualizzate nella scheda **Cartelle di lavoro personali.**


## <a name="create-new-workbook"></a>Crea nuova cartella di lavoro

1. Vai a **Cartelle di lavoro** e quindi seleziona Aggiungi cartella di **lavoro** per creare una nuova cartella di lavoro da zero.
  ![passare alle cartelle di lavoro](./media/tutorial-monitor-data/create-workbook.png)

1. Per modificare la cartella di lavoro, selezionare **Modifica**e quindi aggiungere testo, query e parametri in base alle esigenze. Per altre informazioni su come personalizzare la cartella di lavoro, vedere Creazione di report interattivi con le cartelle di lavoro di Monitoraggio di Azure.For more information on how to customize the workbook, see how to [Create interactive reports with Azure Monitor Workbooks](../azure-monitor/app/usage-workbooks.md). 

1. Quando si compila una query, impostare Origine **dati** su **Registri**, il **tipo di risorsa** è impostato su Log **Analytics** e quindi scegliere le aree di lavoro pertinenti. 

1. Dopo aver creato la cartella di lavoro, salvarla per salvarla nella sottoscrizione e nel gruppo di risorse dell'area di lavoro di Azure Sentinel.

1. Se si desidera consentire ad altri utenti dell'organizzazione di utilizzare la cartella di lavoro, in **Salva per** selezionare **Report condivisi**. Se si desidera che la cartella di lavoro sia disponibile solo per l'utente, selezionare **Report personali**.

1. Per passare da una cartella di lavoro all'altra nell'area di lavoro, è possibile selezionare **Apri** ![Cambia cartella di lavoro](./media/tutorial-monitor-data/switch.png)nel riquadro superiore di qualsiasi cartella di lavoro. Nella finestra che si apre a destra, passare da una cartella di lavoro all'altra.

   ![Passare da una cartella di lavoro all'altra](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Come eliminare le cartelle di lavoro

È possibile eliminare le cartelle di lavoro create da un modello di Azure Sentinel.You can delete Workbooks that were created from an Azure Sentinel template. 

Per eliminare una cartella di lavoro personalizzata, nella pagina Cartelle di lavoro selezionare la cartella di lavoro salvata che si desidera eliminare e scegliere **Elimina**. Questa operazione rimuoverà la cartella di lavoro salvata.

> [!NOTE]
> In questo modo vengono rimosse le risorse e le eventuali modifiche apportate al modello. Il modello originale rimarrà disponibile.

## <a name="next-steps"></a>Passaggi successivi

In this tutorial, you learned how to view your data in Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, vedere [Configurare risposte automatiche alle minacce in Azure Sentinel.](tutorial-respond-threats-playbook.md)
