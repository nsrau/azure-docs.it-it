---
title: includere il file
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121553"
---
Funzioni semplifica l'aggiunta dell'integrazione di Application Insights a un'app per le funzioni dal [portale di Azure].

1. Nel[portale di Azure] [portale di Azure]cercare e selezionare app per le **funzioni**e quindi scegliere l'app per le funzioni. 

1. Selezionare il banner **Application Insights non è configurato** nella parte superiore della finestra. Se non viene visualizzato questo banner, l'app ha già Application Insights abilitata.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Abilitare Application Insights nel portale":::

1. Creare una risorsa di Application Insights usando le impostazioni specificate nella tabella sotto l'immagine.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Creare una risorsa Application Insights":::

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome app univoco | È più facile usare lo stesso nome dell'app per le funzioni, che deve essere univoco nella sottoscrizione. | 
    | **Posizione** | Europa occidentale | Se possibile, usare la stessa [area](https://azure.microsoft.com/regions/) dell'app per le funzioni oppure una vicina a tale area. |

1. Selezionare **Applica**. La risorsa di Application Insights viene creata nella stesso gruppo di risorse e nella stessa sottoscrizione dell'app per le funzioni. Dopo aver creato la risorsa, chiudere la finestra di Application Insights.

1. Tornare all'app per le funzioni, selezionare **Impostazioni**  >  **configurazione**e quindi selezionare **Impostazioni applicazione**. Se viene visualizzata un'impostazione denominata `APPINSIGHTS_INSTRUMENTATIONKEY`, l'integrazione di Application Insights è abilitata per l'app per le funzioni in esecuzione in Azure.

[Portale di Azure]: https://portal.azure.com
