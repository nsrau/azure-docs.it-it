---
title: Distribuire & gestire le istanze del contenitore di AzureDeploy to manage Azure Container Instances
description: Automatizzare le attività e i flussi di lavoro che creano e gestiscono distribuzioni di contenitori nelle istanze del contenitore di Azure usando le app per la logica di AzureAutomate tasks and workflows that create and manage container deployments in Azure Container Instances by using Azure Logic Apps
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
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Distribuire e gestire le istanze del contenitore di Azure usando le app per la logica di AzureDeploy and manage Azure Container Instances by using Azure Logic Apps

Con le app per la logica di Azure e il connettore dell'istanza del contenitore di Azure, è possibile configurare attività e flussi di lavoro automatizzati che distribuiscono e gestiscono gruppi di [contenitori.](../container-instances/container-instances-container-groups.md) Il connettore di istanza contenitore supporta le azioni seguenti:The Container Instance connector supports the following actions:

* Creare o eliminare un gruppo di contenitoriCreate or delete a container group
* Ottenere le proprietà di un gruppo di contenitoriGet the properties of a container group
* Ottenere un elenco di gruppi di contenitoriGet a list of container groups
* Ottenere i log di un'istanza del contenitoreGet the logs of a container instance

Usare queste azioni nelle app per la logica per attività quali l'esecuzione di un carico di lavoro del contenitore in risposta a un trigger di app per la logica. È anche possibile fare in modo che altre azioni utilizzino l'output delle azioni Istanza contenitore.You can also have other actions use the output from Container Instance actions. 

Questo connettore fornisce solo azioni, pertanto per avviare l'app per la logica, usare un trigger separato, ad esempio un trigger **di ricorrenza** per eseguire un carico di lavoro del contenitore in base a una pianificazione regolare. In alternativa, potrebbe essere necessario attivare la distribuzione di un gruppo di contenitori dopo un evento, ad esempio l'arrivo di un messaggio di posta elettronica di Outlook. 

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Informazioni di base su [come creare app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) e su come creare e gestire [istanze del contenitore](../container-instances/container-instances-quickstart.md)

* App per la logica in cui si vuole accedere alle istanze del contenitore. Per usare un'azione, avvia l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza.**

## <a name="add-a-container-instance-action"></a>Aggiungere un'azione Istanza contenitoreAdd a Container Instance action

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

     -oppure-

   * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
   Scegliere il segno**+** più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "istanza contenitore" come filtro. Nell'elenco delle azioni selezionare l'azione connettore dell'istanza contenitore di Azure desiderata.

1. Specificare un nome per la connessione. 

1. Specificare i dettagli necessari per l'azione selezionata e continuare a creare il flusso di lavoro dell'app per la logica.

  Ad esempio, selezionare **Crea gruppo di contenitori** e immettere le proprietà per un gruppo di contenitori e una o più istanze del contenitore nel gruppo, come illustrato nell'immagine seguente (dettaglio parziale):For example, select Create container group and enter the properties for a container group and one or more container instances in the group, as shown in the following image (partial detail):

  ![Creare un gruppo di contenitori](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche sui trigger, le azioni e i limiti descritti dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la pagina di [riferimento](/connectors/aci/) del connettore o il gruppo di contenitori [YAML reference](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Passaggi successivi

* Vedere [un'app per la logica](https://github.com/Azure-Samples/aci-logicapps-integration) di esempio che esegue un contenitore nelle istanze del contenitore di Azure per analizzare il sentiment della posta elettronica o del testo di TwitterSee a sample logic app that runs a container in Azure Container Instances to analyze the sentiment of e-mail or Twitter text

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)