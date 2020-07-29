---
title: Visualizzare i dati con Cartelle di lavoro di Monitoraggio di Azure in Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per informazioni su come visualizzare i dati con le cartelle di lavoro in Azure Sentinel.
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
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 8d8f1343d92f66dc464ab7064949bbabb813268e
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798541"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Esercitazione: Visualizzare e monitorare i dati



Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è possibile visualizzare e monitorare i dati usando la funzionalità Cartelle di lavoro di Monitoraggio di Azure disponibile in Azure Sentinel, che consente di creare dashboard personalizzati in modo versatile. Anche se le cartelle di lavoro sono visualizzate in modo diverso in Azure Sentinel, può essere utile vedere come [creare report interattivi con Cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md). Azure Sentinel consente di creare cartelle di lavoro personalizzate tra i dati e include anche modelli di cartella di lavoro predefiniti per ottenere rapidamente informazioni dettagliate sui dati non appena si connette un'origine dati.


Questa esercitazione consente di visualizzare i dati in Azure Sentinel.
> [!div class="checklist"]
> * Usare cartelle di lavoro predefinite
> * Creare nuove cartelle di lavoro

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere le autorizzazioni come lettore o collaboratore di cartelle di lavoro per il gruppo di risorse dell'area di lavoro di Azure Sentinel.

> [!NOTE]
> Le cartelle di lavoro che è possibile visualizzare in Azure Sentinel vengono salvate nel gruppo di risorse dell'area di lavoro di Azure Sentinel e sono contrassegnate dall'area di lavoro in cui sono state create.

## <a name="use-built-in-workbooks"></a>Usare cartelle di lavoro predefinite

1. Passare a **Cartelle di lavoro** e quindi selezionare **Modelli** per visualizzare l'elenco completo delle cartelle di lavoro predefinite di Azure Sentinel. Per sapere quali sono pertinenti per i tipi di dati connessi, il campo **Tipi di dati obbligatori** in ogni cartella di lavoro elenca il tipo di dati accanto a un segno di spunta verde se i dati rilevanti sono già stati trasmessi ad Azure Sentinel.
  ![Passare a Cartelle di lavoro](./media/tutorial-monitor-data/access-workbooks.png)
1. Fare clic su **Visualizza la cartella di lavoro** per visualizzare il modello popolato con i dati.
  
1. Per modificare la cartella di lavoro, selezionare **Salva** e quindi selezionare il percorso in cui si vuole salvare il file JSON per il modello. 

   > [!NOTE]
   > Viene quindi creata una risorsa di Azure basata sul modello pertinente e viene salvato il file JSON del modello e non i dati.


1. Selezionare **Visualizza la cartella di lavoro**. Fare quindi clic sul pulsante **Modifica** nella parte superiore della schermata. A questo punto è possibile modificare la cartella di lavoro e personalizzarla in base alle esigenze. Per altre informazioni su come personalizzare la cartella di lavoro, vedere [Creare report interattivi con Cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md).
![Visualizza la cartella di lavoro](./media/tutorial-monitor-data/workbook-graph.png)
1. Dopo avere apportato le modifiche, è possibile salvare la cartella di lavoro. 

1. È anche possibile clonare la cartella di lavoro: Selezionare **Modifica** e quindi **Salva con nome**, assicurandosi di salvarlo con un altro nome, nella stessa sottoscrizione e nello stesso gruppo di risorse. Queste cartelle di lavoro vengono visualizzate nella scheda **Cartelle di lavoro personali**.


## <a name="create-new-workbook"></a>Creare una nuova cartella di lavoro

1. Per creare una nuova cartella di lavoro, passare a **Cartelle di lavoro** e quindi selezionare **Aggiungi la cartella di lavoro**.
  ![Passare a Cartelle di lavoro](./media/tutorial-monitor-data/create-workbook.png)

1. Per modificare la cartella di lavoro, selezionare **Modifica** e quindi aggiungere testo, query e parametri, se necessari. Per altre informazioni su come personalizzare la cartella di lavoro, vedere [Creare report interattivi con Cartelle di lavoro di Monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md). 

1. Quando si crea una query, impostare **Origine dati** su **Log**, **Tipo di risorsa** su **Analisi log** e quindi scegliere le aree di lavoro pertinenti. 

1. Dopo aver creato la cartella di lavoro, salvarla prestando attenzione a salvarla nella sottoscrizione e nel gruppo di risorse dell'area di lavoro di Azure Sentinel.

1. Se si vuole consentire ad altri utenti dell'organizzazione di usare la cartella di lavoro, in **Salva in** selezionare **Report condivisi**. Se si vuole che la cartella di lavoro non sia disponibile per altri utenti, selezionare **Report personali**.

1. Per passare da una cartella di lavoro all'altra nell'area di lavoro, è possibile selezionare **Apri** ![Passare a un'altra cartella di lavoro](./media/tutorial-monitor-data/switch.png)nel riquadro superiore di qualsiasi cartella di lavoro. Nella finestra visualizzata a destra cambiare la cartella di lavoro attiva.

   ![Passare a un'altra cartella di lavoro](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Come eliminare le cartelle di lavoro

È possibile eliminare le cartelle di lavoro create da un modello di Azure Sentinel. 

Per eliminare una cartella di lavoro personalizzata, nella pagina Cartelle di lavoro selezionare la cartella di lavoro salvata che si vuole eliminare e selezionare **Elimina**. La cartella di lavoro salvata verrà rimossa.

> [!NOTE]
> Oltre alla risorsa verranno rimosse anche tutte le modifiche apportate al modello, mentre il modello originale rimarrà disponibile.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come visualizzare i dati in Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, vedere [Configurare le risposte automatiche alle minacce in Azure Sentinel](tutorial-respond-threats-playbook.md).
