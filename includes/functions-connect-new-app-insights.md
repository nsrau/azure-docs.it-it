---
title: includere il file
description: includere file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731017"
---
Funzioni di Azure consente di aggiungere in modo semplice Application Insights integrazione a un'app per le funzioni dall' [portale di Azure].

1. Nel[portale di Azure] [portale di Azure]cercare e selezionare app per le **funzioni**e quindi scegliere l'app per le funzioni. 

1. Selezionare il banner **Application Insights non è configurato** nella parte superiore della finestra. Se non viene visualizzato questo banner, è possibile che l'app abbia già abilitato Application Insights.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Abilitare Application Insights nel portale":::

1. Espandere **modifica la risorsa** e creare una risorsa Application Insights usando le impostazioni specificate nella tabella seguente.  

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nuovo nome risorsa** | Nome app univoco | È più facile usare lo stesso nome dell'app per le funzioni, che deve essere univoco nella sottoscrizione. | 
    | **Posizione** | Europa occidentale | Se possibile, usare la stessa [area](https://azure.microsoft.com/regions/) dell'app per le funzioni oppure una vicina a tale area. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Creare una risorsa di Application Insights":::

1. Selezionare **Applica**. 

   La risorsa di Application Insights viene creata nella stesso gruppo di risorse e nella stessa sottoscrizione dell'app per le funzioni. Dopo aver creato la risorsa, chiudere la finestra di Application Insights.

1. Nell'app per le funzioni selezionare **configurazione** in **Impostazioni**e quindi selezionare **Impostazioni applicazione**. Se viene visualizzata un'impostazione denominata `APPINSIGHTS_INSTRUMENTATIONKEY`, l'integrazione di Application Insights è abilitata per l'app per le funzioni in esecuzione in Azure.

[Portale di Azure]: https://portal.azure.com
