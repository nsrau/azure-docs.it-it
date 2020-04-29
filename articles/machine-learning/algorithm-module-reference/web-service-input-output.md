---
title: Input/output del servizio Web
description: Informazioni sui moduli del servizio Web in Azure Machine Learning Designer (anteprima)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462087"
---
# <a name="web-service-inputoutput"></a>Input/output del servizio Web

Questo articolo descrive il modulo di **input del servizio** Web e il modulo di **output del servizio Web** in Azure Machine Learning Designer (anteprima).

Il modulo di **input del servizio Web** può connettersi solo a una porta di input di tipo **DataFrameDirectory**. E il modulo di **output del servizio Web** può essere connesso solo dalla porta di output di tipo **DataFrameDirectory**. I due moduli sono disponibili nell'albero del modulo, in categoria **servizio Web** . 

Il modulo di **input del servizio Web** viene usato per indicare la posizione in cui i dati utente vengono inseriti nella pipeline e il modulo di **output del servizio Web** viene usato per indicare dove vengono restituiti i dati utente in una pipeline di inferenza in tempo reale

## <a name="how-to-use-web-service-inputoutput"></a>Come usare input/output del servizio Web

- Quando si crea una pipeline di inferenza in tempo reale dalla pipeline di training, viene automaticamente aggiunto il modulo di **input del servizio Web** e di **output del servizio Web** per mostrare dove i dati utente entrano nella pipeline e dove vengono restituiti i dati. 

    Altre informazioni su come [creare una pipeline di inferenza in tempo reale](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > La generazione automatica della pipeline di inferenza in tempo reale è un processo basato su regole ottimali che non garantisce la correttezza. È possibile aggiungere o rimuovere manualmente moduli di **input/output del servizio Web** per soddisfare i requisiti. Verificare che sia presente almeno un modulo di **input del servizio Web** e un modulo di **output del servizio Web** nella pipeline di inferenza in tempo reale. Se sono presenti più moduli di **input del servizio Web** o di **output del servizio Web** , assicurarsi che abbiano nomi univoci, che possono essere inseriti nel riquadro destro del modulo.

- È anche possibile creare manualmente una pipeline di inferenza in tempo reale aggiungendo moduli di **input del servizio Web** e di **output del servizio Web** alla pipeline non inviata.

    > [!NOTE]
    >  Il tipo di pipeline verrà determinato al momento della prima invio. Assicurarsi quindi di aggiungere il modulo di **input del servizio** Web e di **output del servizio Web** prima di inviare per la prima volta se si vuole creare una pipeline di inferenza in tempo reale.

   Nell'esempio seguente viene illustrato come creare manualmente la pipeline di inferenza in tempo reale dal modulo **Execute Python script** . 

   ![Esempio](media/module/web-service-input-output-example.png)
   
   Dopo aver inviato la pipeline e l'esecuzione è stata completata correttamente, sarà possibile distribuire l'endpoint in tempo reale.
   
   > [!NOTE]
   >  Nell'esempio precedente, **immettere i dati manualmente** fornisce lo schema di dati per l'input del servizio Web ed è necessario per la distribuzione dell'endpoint in tempo reale. In genere, è necessario connettere sempre un modulo o un set di dati alla porta a cui è connesso l' **input del servizio Web** per fornire lo schema di dati.
   
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [distribuire l'endpoint in tempo reale](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.