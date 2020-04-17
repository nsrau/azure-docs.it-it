---
title: Input/output del servizio Web
description: Informazioni sui moduli di servizio Web in Progettazione Azure Machine Learning (anteprima)Learn about the web service modules in Azure Machine Learning designer (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462087"
---
# <a name="web-service-inputoutput"></a>Input/output del servizio Web

Questo articolo descrive il modulo **Input servizio Web** e il modulo Output servizio **Web** nella finestra di progettazione di Azure Machine Learning (anteprima).

**Il** modulo Input servizio Web può connettersi alla porta di input solo con tipo **DataFrameDirectory**. E il modulo **di output** del servizio Web può essere connesso solo dalla porta di output con tipo **DataFrameDirectory**. I due moduli sono disponibili nell'albero dei moduli, nella categoria **Servizio Web.** 

Il modulo **Input servizio Web** viene utilizzato per indicare dove i dati utente entrano nella pipeline e il modulo Output servizio **Web** viene utilizzato per indicare dove vengono restituiti i dati utente in una pipeline di inferenza in tempo reale.

## <a name="how-to-use-web-service-inputoutput"></a>Come utilizzare l'input/output del servizio Web

- Quando si crea una pipeline di inferenza in tempo reale dalla pipeline di training, il modulo **Input servizio Web** e Output servizio **Web** verrà aggiunto automaticamente per mostrare dove i dati utente entrano nella pipeline e dove vengono restituiti i dati. 

    Ulteriori informazioni sulla creazione di una pipeline di [inferenza in tempo reale](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > La generazione automatica della pipeline di inferenza in tempo reale è un processo di best effort basato su regole, non è garantito la correttezza. È possibile aggiungere o rimuovere manualmente i moduli **Di input/output del servizio Web** per soddisfare le proprie esigenze. Assicurarsi che nella pipeline di inferenza in tempo reale siano presenti almeno un modulo **Input servizio Web** e un modulo Output servizio **Web.** Se si dispone di più moduli **Web Service Input** o Web Service **Output,** assicurarsi che abbiano nomi univoci, che è possibile immettere il nome nel riquadro destro del modulo.

- È inoltre possibile creare manualmente una pipeline di inferenza in tempo reale aggiungendo i moduli **Input servizio Web** e Output servizio **Web** alla pipeline non inviata.

    > [!NOTE]
    >  Il tipo di pipeline verrà determinato al primo invio. Assicurarsi quindi di aggiungere il modulo **Input servizio Web** e Output servizio **Web** prima di inviare per la prima volta se si desidera creare una pipeline di inferenza in tempo reale.

   Di seguito viene illustrato come creare manualmente la pipeline di inferenza in tempo reale dal modulo **Esegui script Python.** 

   ![Esempio](media/module/web-service-input-output-example.png)
   
   Dopo aver inviato la pipeline e l'esecuzione viene completata correttamente, sarà possibile distribuire l'endpoint in tempo reale.
   
   > [!NOTE]
   >  Nell'esempio precedente, **Immettere** i dati manualmente fornisce lo schema dei dati per l'input del servizio Web ed è necessario per la distribuzione dell'endpoint in tempo reale. In genere, è sempre necessario connettere un modulo o un set di dati alla porta a cui è connesso **l'input** del servizio Web per fornire lo schema dei dati.
   
## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla [distribuzione dell'endpoint in tempo reale.](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)
Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning.