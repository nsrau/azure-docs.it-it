---
title: 'Input/output del servizio Web: riferimento al modulo'
description: Informazioni sui moduli del servizio Web in Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: e1bf5dd070ac12878c608f744ff450395de84c48
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310029"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Moduli di input del servizio Web e di output del servizio Web

Questo articolo descrive i moduli di input del servizio Web e di output del servizio Web in Azure Machine Learning Designer.

Il modulo di input del servizio Web può connettersi solo a una porta di input con il tipo **DataFrameDirectory**. Il modulo di output del servizio Web può essere connesso solo da una porta di output di tipo **DataFrameDirectory**. Nella categoria **servizio Web** è possibile trovare i due moduli nell'albero del modulo. 

Il modulo di input del servizio Web indica il punto in cui i dati utente entrano nella pipeline. Il modulo di output del servizio Web indica dove vengono restituiti i dati utente in una pipeline di inferenza in tempo reale.

## <a name="how-to-use-web-service-input-and-output"></a>Come usare l'input e l'output del servizio Web

Quando si [Crea una pipeline di inferenza in tempo reale](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) dalla pipeline di training, i moduli di input del servizio Web e di output del servizio Web verranno aggiunti automaticamente per mostrare dove i dati utente entrano nella pipeline e dove vengono restituiti i dati. 

> [!NOTE]
> La generazione automatica di una pipeline di inferenza in tempo reale è un processo basato su regole che prevede il massimo sforzo. Non esiste alcuna garanzia di correttezza. 

È possibile aggiungere o rimuovere manualmente i moduli di input del servizio Web e di output del servizio Web per soddisfare i requisiti. Assicurarsi che la pipeline di inferenza in tempo reale includa almeno un modulo di input del servizio Web e un modulo di output del servizio Web. Se sono presenti più moduli di input del servizio Web o di output del servizio Web, assicurarsi che abbiano nomi univoci. È possibile immettere il nome nel riquadro di destra del modulo.

È anche possibile creare manualmente una pipeline di inferenza in tempo reale aggiungendo moduli di input del servizio Web e di output del servizio Web alla pipeline non inviata.

> [!NOTE]
> Il tipo di pipeline verrà determinato la prima volta che lo si invia. Assicurarsi di aggiungere i moduli di input del servizio Web e di output del servizio Web prima di inviare per la prima volta.

L'esempio seguente illustra come creare manualmente una pipeline di inferenza in tempo reale dal modulo Execute Python script. 

![Esempio](media/module/web-service-input-output-example.png)
   
Dopo aver inviato la pipeline e completato l'esecuzione, è possibile distribuire l'endpoint in tempo reale.
   
> [!NOTE]
>  Nell'esempio precedente, l' **immissione manuale dei dati** fornisce lo schema di dati per l'input del servizio Web ed è necessario per la distribuzione dell'endpoint in tempo reale. In genere, è necessario connettere sempre un modulo o un set di dati alla porta in cui l' **input del servizio Web** è connesso per fornire lo schema di dati.
   
## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [distribuzione dell'endpoint in tempo reale](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.