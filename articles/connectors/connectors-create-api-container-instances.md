---
title: Distribuire & gestire istanze di contenitore di Azure
description: Automatizzare le attività e i flussi di lavoro che creano e gestiscono le distribuzioni di contenitori in istanze di contenitore di Azure usando app per la logica di Azure
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046292"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Distribuire e gestire istanze di contenitore di Azure usando app per la logica di Azure

Con le app per la logica di Azure e il connettore dell'istanza di contenitore di Azure è possibile configurare attività e flussi di lavoro automatizzati che distribuiscono e gestiscono i [gruppi di contenitori](../container-instances/container-instances-container-groups.md). Il connettore dell'istanza del contenitore supporta le azioni seguenti:

* Creare o eliminare un gruppo di contenitori
* Ottenere le proprietà di un gruppo di contenitori
* Ottenere un elenco di gruppi di contenitori
* Ottenere i log di un'istanza di contenitore

Usare queste azioni nelle app per la logica per attività come l'esecuzione di un carico di lavoro del contenitore in risposta a un trigger di app per la logica. È anche possibile che altre azioni usino l'output delle azioni dell'istanza di contenitore. 

Questo connettore fornisce solo azioni, quindi, per avviare l'app per la logica, usare un trigger separato, ad esempio un trigger di **ricorrenza** per eseguire un carico di lavoro del contenitore in base a una pianificazione regolare. In alternativa, potrebbe essere necessario attivare una distribuzione del gruppo di contenitori dopo un evento, ad esempio l'arrivo di un messaggio di posta elettronica di Outlook. 

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Informazioni di base su [come creare app](../logic-apps/quickstart-create-first-logic-app-workflow.md) per la logica e su [come creare e gestire istanze di contenitore](../container-instances/container-instances-quickstart.md)

* App per la logica in cui si vuole accedere alle istanze del contenitore. Per usare un'azione, avviare l'app per la logica con un altro trigger, ad esempio il trigger di **ricorrenza** .

## <a name="add-a-container-instance-action"></a>Aggiungere un'azione dell'istanza di contenitore

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

     -oppure-

   * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
   Scegliere il segno più (**+**) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "istanza contenitore" come filtro. Nell'elenco azioni selezionare l'azione del connettore dell'istanza di contenitore di Azure desiderata.

1. Specificare un nome per la connessione. 

1. Specificare i dettagli necessari per l'azione selezionata e continuare a creare il flusso di lavoro dell'app per la logica.

  Ad esempio, selezionare **Crea gruppo di contenitori** e immettere le proprietà per un gruppo di contenitori e una o più istanze di contenitore nel gruppo, come illustrato nella figura seguente (dettagli parziali):

  ![Creare un gruppo di contenitori](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione del connettore OpenAPI (in precedenza spavalderia), vedere la [pagina di riferimento](/connectors/aci/) del connettore o il [riferimento YAML](../container-instances/container-instances-reference-yaml.md)del gruppo di contenitori.

## <a name="next-steps"></a>Passaggi successivi

* Vedere un' [app](https://github.com/Azure-Samples/aci-logicapps-integration) per la logica di esempio che esegue un contenitore in istanze di contenitore di Azure per analizzare i sentimenti del testo di posta elettronica o Twitter

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)