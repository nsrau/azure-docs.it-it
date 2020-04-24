---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132603"
---
Funzioni semplifica l'aggiunta dell'integrazione di Application Insights a un'app per le funzioni dal [portale di Azure].

1. Nel portale [portal]di[Azure]portale, digitare `Function Apps` nella barra di ricerca nella parte superiore della pagina, scegliere l'app per le funzioni e quindi selezionare il banner **Application Insights non è configurato** nella parte superiore della finestra. Se non viene visualizzato questo banner, l'app ha già Application Insights abilitata.

    ![Abilitare Application Insights nel portale](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Creare una risorsa di Application Insights usando le impostazioni specificate nella tabella sotto l'immagine.

   ![Creare una risorsa di Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome app univoco | È più facile usare lo stesso nome dell'app per le funzioni, che deve essere univoco nella sottoscrizione. | 
    | **Posizione** | Europa occidentale | Se possibile, usare la stessa [area](https://azure.microsoft.com/regions/) dell'app per le funzioni oppure una vicina a tale area. |

1. Fare clic su **OK**. La risorsa di Application Insights viene creata nella stesso gruppo di risorse e nella stessa sottoscrizione dell'app per le funzioni. Dopo aver creato la risorsa, chiudere la finestra di Application Insights.

1. Nell'app per le funzioni selezionare **Impostazioni applicazione** e scorrere fino a **Impostazioni applicazione**. Se viene visualizzata un'impostazione denominata `APPINSIGHTS_INSTRUMENTATIONKEY`, l'integrazione di Application Insights è abilitata per l'app per le funzioni in esecuzione in Azure.

[Portale di Azure]: https://portal.azure.com
