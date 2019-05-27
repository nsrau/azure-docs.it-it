---
title: File di inclusione
description: File di inclusione
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131550"
---
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **+ crea una risorsa** sul lato sinistro, quindi scegliere **app per le funzioni**.

1. Per la **piano di Hosting**, scegliere **piano di servizio App**, quindi selezionare **piano di servizio App/località**.

    ![Creare un'app per le funzioni](./media/functions-premium-create/create-function-app-resource.png)

1. Selezionare **Crea nuovo**, tipo di un' **piano di servizio App** assegnare un nome, scegliere un **posizione** in un [area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino altra delle funzioni di servizi accedere e quindi selezionare **tariffario**.

    ![Creare un piano di servizio app](./media/functions-premium-create/new-app-service-plan.png)

1. Scegliere il **EP1** (database elastico Premium) piano, quindi selezionare **applica**.

    ![Selezionare il piano premium](./media/functions-premium-create/hosting-plan.png) 

1. Selezionare **OK** per creare il piano, quindi usare le impostazioni di app di funzione rimanenti come specificato nella tabella sotto l'immagine. 

    ![Piano di servizio app finita](./media/functions-premium-create/create-function-app.png)  

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome app** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z`, `0-9` e `-`.  | 
    | **Sottoscrizione** | Sottoscrizione | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **[Gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. È anche possibile usare il valore suggerito. |
    | **OS** | Windows | Linux non è attualmente supportato nel piano Premium. |
    | **Stack di runtime** | Lingua preferita | Scegliere un runtime che supporti il linguaggio di programmazione della funzione preferito. Scegliere **.NET** per le funzioni C# e F #. Solo i linguaggi supportati nella prescelta **OS** vengono visualizzati. |
    | **[Archiviazione](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che dovrà soddisfare i [requisiti dell'account di archiviazione](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinito | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione, è possibile cambiare il **Nome nuova risorsa** oppure scegliere una **Località** in un'[area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vogliono archiviare i dati. |

1. Dopo che le impostazioni vengono convalidate, selezionare **Create**.

1. Selezionare l'icona di notifica nell'angolo superiore destro del portale e attendere la visualizzazione del messaggio **La distribuzione è riuscita**.

    ![Definire nuove impostazioni dell'app per le funzioni](./media/functions-premium-create/function-app-create-notification.png)

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.