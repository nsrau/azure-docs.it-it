---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179910"
---
Funzioni semplifica l'aggiunta dell'integrazione di Application Insights a un'app per le funzioni dal [portale di Azure].

1. Nel [portale][portale di Azure], selezionare **Tutti i servizi > App per le funzioni**, selezionare la propria app per le funzioni, quindi scegliere il banner **Application Insights** in alto nella finestra

    ![Abilitare Application Insights nel portale](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Creare una risorsa di Application Insights usando le impostazioni specificate nella tabella sotto l'immagine:

   ![Creare una risorsa di Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome app univoco | È più facile usare lo stesso nome dell'app per le funzioni, che deve essere univoco nella sottoscrizione. | 
    | **Posizione** | Europa occidentale | Se possibile, usare la stessa [regione](https://azure.microsoft.com/regions/) dell'app per le funzioni o una limitrofa. |

1. Scegliere **OK**. La risorsa di Application Insights viene creata nella stesso gruppo di risorse e nella stessa sottoscrizione dell'app per le funzioni. Dopo aver completato la creazione, chiudere la finestra di Application Insights.

1. Nell'app per le funzioni, selezionare **Impostazioni applicazione** e scorrere fino a **Impostazioni applicazione**. Quando viene visualizzata un'impostazione denominata `APPINSIGHTS_INSTRUMENTATIONKEY`, significa che l'integrazione di Application Insights è abilitata per l'app per le funzioni in esecuzione in Azure.

[Portale di Azure]: https://portal.azure.com
